**原因：**

1. 当浏览器检测到**非用户操作**产生的新弹出窗口，则会对其进行阻止；
   因为浏览器认为这可能是一个广告，不是一个用户希望看到的页面；
2. 对于向后台**发送请求之后的打开**，都认为是非用户操作，部分浏览器就会自动拦截掉；

**解决：**

```js
<a @click="jump('yuejuan')" target="_blank">阅卷</a>
 
 
// click 事件
jump(val) {
      if (val) {
        const params = {
          type: 2
        };
        // 在一步异步发起前开启新窗口
        const winOpen = window.open('', '_blank');
        this.$http.post('/teacher-v2/redirect-url', params)
          .then((resp) => this.$checkResp(resp))
          .then((data) => {
            if (data) {
              // 替换
              winOpen.location.href = data.url;
            }
          })
          .catch(() => {
            this.$message.warning('跳转失败');
          });
      }
 }
```

