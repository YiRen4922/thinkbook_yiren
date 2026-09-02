逻辑跳过，-1被转化为unsigned最大值，必然返回false
![[Screenshot_20260903_011800_tv_danmaku_bili_UnitedBizDetailsActivity.jpg|280x125]]

CWE-190
负数n被隐式转换为size_t，申请极大内存击穿内存寻址上限，造成分配失败或段错误，或溢出申请极小内存，造成堆溢出
![[Screenshot_20260903_011303_tv_danmaku_bili_UnitedBizDetailsActivity.jpg|280x125]]

![[Screenshot_20260903_011607_tv_danmaku_bili_UnitedBizDetailsActivity.jpg]]