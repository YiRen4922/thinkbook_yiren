

下面是deepseek写的轻量json解析器
```c++
#include <iostream>
#include <variant>
#include <vector>
#include <map>
#include <string>
#include <string_view>
#include <optional>
#include <cctype>
#include <stdexcept>

// ---------- JSON 值的类型定义 ----------
class JsonValue {
public:
    using Object = std::map<std::string, JsonValue>;
    using Array  = std::vector<JsonValue>;

    // 六种 JSON 类型，用 variant 存储
    using Variant = std::variant<
        std::nullptr_t,   // null
        bool,             // true/false
        double,           // number
        std::string,      // string
        Array,            // array
        Object            // object
    >;

    JsonValue() = default;
    JsonValue(Variant v) : data_(std::move(v)) {}

    // 类型判断
    bool isNull()   const { return std::holds_alternative<std::nullptr_t>(data_); }
    bool isBool()   const { return std::holds_alternative<bool>(data_); }
    bool isNumber() const { return std::holds_alternative<double>(data_); }
    bool isString() const { return std::holds_alternative<std::string>(data_); }
    bool isArray()  const { return std::holds_alternative<Array>(data_); }
    bool isObject() const { return std::holds_alternative<Object>(data_); }

    // 安全取值（若类型不匹配则抛出异常）
    bool        asBool()   const { return std::get<bool>(data_); }
    double      asNumber() const { return std::get<double>(data_); }
    const std::string& asString() const { return std::get<std::string>(data_); }
    const Array&  asArray()  const { return std::get<Array>(data_); }
    const Object& asObject() const { return std::get<Object>(data_); }

    // 下标访问（仅对 object 和 array 生效）
    JsonValue& operator[](const std::string& key) {
        return std::get<Object>(data_)[key];
    }
    const JsonValue& operator[](const std::string& key) const {
        return std::get<Object>(data_).at(key);
    }
    JsonValue& operator[](size_t index) {
        return std::get<Array>(data_)[index];
    }
    const JsonValue& operator[](size_t index) const {
        return std::get<Array>(data_).at(index);
    }

    // 获取原始 variant（方便遍历）
    const Variant& data() const { return data_; }

private:
    Variant data_;
};

// ---------- 词法分析 (Tokenizer) ----------
enum class TokenType {
    T_BEGIN_OBJECT,  // {
    T_END_OBJECT,    // }
    T_BEGIN_ARRAY,   // [
    T_END_ARRAY,     // ]
    T_COLON,         // :
    T_COMMA,         // ,
    T_STRING,
    T_NUMBER,
    T_TRUE,
    T_FALSE,
    T_NULL,
    T_EOF
};

struct Token {
    TokenType type;
    std::string_view literal;  // 原始字符串片段
};

class Tokenizer {
public:
    explicit Tokenizer(std::string_view input) : input_(input), pos_(0) {}

    // 获取下一个 Token（忽略空白）
    Token nextToken() {
        skipWhitespace();
        if (pos_ >= input_.size()) return {TokenType::T_EOF, {}};

        char c = input_[pos_];
        switch (c) {
            case '{': ++pos_; return {TokenType::T_BEGIN_OBJECT, "{"};
            case '}': ++pos_; return {TokenType::T_END_OBJECT, "}"};
            case '[': ++pos_; return {TokenType::T_BEGIN_ARRAY, "["};
            case ']': ++pos_; return {TokenType::T_END_ARRAY, "]"};
            case ':': ++pos_; return {TokenType::T_COLON, ":"};
            case ',': ++pos_; return {TokenType::T_COMMA, ","};
            case '"': return parseString();
            case '-': case '0': case '1': case '2': case '3': case '4':
            case '5': case '6': case '7': case '8': case '9':
                return parseNumber();
            case 't': return parseKeyword("true", TokenType::T_TRUE);
            case 'f': return parseKeyword("false", TokenType::T_FALSE);
            case 'n': return parseKeyword("null", TokenType::T_NULL);
            default:
                throw std::runtime_error("Unexpected character");
        }
    }

private:
    void skipWhitespace() {
        while (pos_ < input_.size() && std::isspace(input_[pos_])) ++pos_;
    }

    Token parseString() {
        ++pos_; // skip opening "
        size_t start = pos_;
        while (pos_ < input_.size() && input_[pos_] != '"') {
            if (input_[pos_] == '\\') ++pos_; // 简单跳过转义，实际需处理
            ++pos_;
        }
        if (pos_ >= input_.size()) throw std::runtime_error("Unterminated string");
        std::string_view str = input_.substr(start, pos_ - start);
        ++pos_; // skip closing "
        return {TokenType::T_STRING, str};
    }

    Token parseNumber() {
        size_t start = pos_;
        // 简单识别整数/浮点，未处理科学计数法但可扩展
        while (pos_ < input_.size() && (std::isdigit(input_[pos_]) || input_[pos_] == '.' || input_[pos_] == 'e' || input_[pos_] == 'E' || input_[pos_] == '+' || input_[pos_] == '-')) {
            ++pos_;
        }
        return {TokenType::T_NUMBER, input_.substr(start, pos_ - start)};
    }

    Token parseKeyword(std::string_view expected, TokenType type) {
        if (input_.substr(pos_, expected.size()) == expected) {
            pos_ += expected.size();
            return {type, expected};
        }
        throw std::runtime_error("Invalid keyword");
    }

    std::string_view input_;
    size_t pos_;
};

// ---------- 语法分析 (Parser) ----------
class Parser {
public:
    explicit Parser(std::string_view input) : tokenizer_(input) {
        current_ = tokenizer_.nextToken();
    }

    JsonValue parse() {
        auto val = parseValue();
        // 如果后面还有非空白，说明有残余字符
        auto next = tokenizer_.nextToken();
        if (next.type != TokenType::T_EOF)
            throw std::runtime_error("Extra characters after JSON value");
        return val;
    }

private:
    Tokenizer tokenizer_;
    Token current_;

    void advance() { current_ = tokenizer_.nextToken(); }

    JsonValue parseValue() {
        switch (current_.type) {
            case TokenType::T_BEGIN_OBJECT: return parseObject();
            case TokenType::T_BEGIN_ARRAY:  return parseArray();
            case TokenType::T_STRING: {
                JsonValue val(std::string(current_.literal));
                advance();
                return val;
            }
            case TokenType::T_NUMBER: {
                double num = std::stod(std::string(current_.literal));
                advance();
                return JsonValue(num);
            }
            case TokenType::T_TRUE:
                advance();
                return JsonValue(true);
            case TokenType::T_FALSE:
                advance();
                return JsonValue(false);
            case TokenType::T_NULL:
                advance();
                return JsonValue(nullptr);
            default:
                throw std::runtime_error("Unexpected token");
        }
    }

    JsonValue parseObject() {
        advance(); // skip {
        JsonValue::Object obj;
        if (current_.type == TokenType::T_END_OBJECT) {
            advance();
            return JsonValue(obj);
        }
        while (true) {
            if (current_.type != TokenType::T_STRING)
                throw std::runtime_error("Expected string key");
            std::string key(current_.literal);
            advance();
            if (current_.type != TokenType::T_COLON)
                throw std::runtime_error("Expected colon");
            advance();
            JsonValue value = parseValue();
            obj.emplace(std::move(key), std::move(value));
            if (current_.type == TokenType::T_COMMA) {
                advance();
            } else if (current_.type == TokenType::T_END_OBJECT) {
                advance();
                break;
            } else {
                throw std::runtime_error("Expected comma or }");
            }
        }
        return JsonValue(obj);
    }

    JsonValue parseArray() {
        advance(); // skip [
        JsonValue::Array arr;
        if (current_.type == TokenType::T_END_ARRAY) {
            advance();
            return JsonValue(arr);
        }
        while (true) {
            arr.push_back(parseValue());
            if (current_.type == TokenType::T_COMMA) {
                advance();
            } else if (current_.type == TokenType::T_END_ARRAY) {
                advance();
                break;
            } else {
                throw std::runtime_error("Expected comma or ]");
            }
        }
        return JsonValue(arr);
    }
};

// ---------- 使用示例 ----------
int main() {
    try {
        std::string json = R"({
            "name": "Alice",
            "age": 30,
            "is_student": false,
            "scores": [95, 87, 92],
            "address": {
                "city": "Beijing",
                "zip": 100000
            }
        })";

        Parser parser(json);
        JsonValue root = parser.parse();

        // 访问数据
        std::cout << "Name: " << root["name"].asString() << "\n";
        std::cout << "Age: " << root["age"].asNumber() << "\n";
        std::cout << "Is student? " << std::boolalpha << root["is_student"].asBool() << "\n";
        std::cout << "Second score: " << root["scores"][1].asNumber() << "\n";
        std::cout << "City: " << root["address"]["city"].asString() << "\n";
    } catch (const std::exception& e) {
        std::cerr << "Error: " << e.what() << "\n";
    }
    return 0;
}
```