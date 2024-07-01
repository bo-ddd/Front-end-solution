# 解决单页面应用中弹层在移动端应用中手机默认的划屏回退事件中弹层无法关闭的问题
> 技术栈： angular, ionic

## 解决方案
```javascript
  // modal.component.ts
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

  // page.component.ts
  import ModalAComponent from './component/modalA.compnent';
  export default class PageComponent {
    constructor(private modalCtrl: ModalController){}
    async openModal(): Promise<void> {
      const modal = await this.modalCtrl.create({
        component: ModalAComponent,
        componentProps: {
          // ... 传给弹层组件的参数
        }
      })
      modal.present();
      const { data, status } = await modal.onWillDismiss();
      if(status == 'confirm') {
        console.log(data);  // 弹层传过来的参数
      } else if (status == 'cancel') {
        // 取消/关闭弹层
      }
    }
  }

// page.component.html
 <div (click)='openModal()'>打开弹层</div>

// modalA.component.ts
export class ModalAComponent exntends ModalComponent {
  constructor(private modalCtrl: ModalController) {
    super();
  }

  confirm(): void {
    this.modalCtrl.dismiss(null, 'confirm');
  }

  cancel(): void {
    this.modalCtrl.dismiss(null, 'cancel');
  }
}
```
