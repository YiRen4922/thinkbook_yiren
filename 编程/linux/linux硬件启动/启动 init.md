如果是 Buildroot：

```
/sbin/init
```

可能最终是 BusyBox init。

如果是很多发行版：

```
/sbin/init
   ↓
systemd
```