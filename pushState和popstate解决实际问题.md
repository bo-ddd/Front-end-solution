# 解决单页面应用中弹层在移动端应用中手机默认的划屏回退事件中弹层无法关闭的问题
> 技术栈： angular, ionic

## 解决方案
```javascript
  // 模态框的基类
  export default class Modal {
    constructor() {
      history.pushState({isModal: true}, '', location.href + location.hash);
    }
  }

  // app.component.ts
  export default class AppComponent implements OnInit  {
    constructor(private modalCtrl: ModalController){}

    ngOnint() {
      window.addEventListener('popstate', event => {
        if(event.state.isModal){
          this.modalCtrl.getTop().then(res => {
            res.dismiss();
          })
        }
      })
    }
  }
```
