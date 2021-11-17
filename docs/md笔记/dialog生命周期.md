# Dialog的生命周期

## 问题记录：

1. 定义dialog

```java
public class ProceedDialog extends Dialog {
  public ProceedDialog(Context context, String type) {
        super(context);
        this.type = type;
        binding = DialogProceedBinding.inflate(LayoutInflater.from(context));
        if (LOG_IN.equals(type)) {
            proceedBeanList = getLogInData();
        } else if (NOT_LOG_IN.equals(type)) {
            proceedBeanList = getLogOutData();
        }
    }
   @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        getWindow().setBackgroundDrawable(new ColorDrawable(Color.TRANSPARENT));
        setCancelable(false);
        setContentView(binding.getRoot());
        proceedAdapter = new ProceedAdapter(R.layout.item_proceed, proceedBeanList);
        LinearLayoutManager layoutManager = new LinearLayoutManager(getContext());
        binding.rvDialogSelects.setLayoutManager(layoutManager);
        binding.rvDialogSelects.setAdapter(proceedAdapter);
      ...//点击事件监听...
    }
  //设置数据
  public void goneNotConfig() {
        if (proceedBeanList!=null && proceedAdapter != null) {
            if (proceedBeanList.size() > 3) {
                proceedBeanList.remove(0);
                proceedBeanList.remove(1);
            }
            proceedAdapter.notifyDataSetChanged();
        }
    }
}
```

2. 调用

   ```java
   proceedDialog.goneNotConfig();
   showProceedDialog();
   ```

   

如代码段所示，在一个菜单列表弹窗中，先设置了 `proceedAdapter.notifyDataSetChanged();` 后，调用了 `show()` 方法。于是编译器在  `proceedAdapter.notifyDataSetChanged();` 抛出了空值异常。

只能怀疑是不是 `onCreate()` 方法是不是在 `show()` 方法之后调用的。

## dialog 的生命周期

Dialog的生命周期一共用以下6个方法： 
onCreate()，show()，onStart() ，cancel()，onDismiss()，Stop() 。

1. Dialog仅在在第一次启动时候会执行onCreate（）方法（之后无论该Dialog执行Dismiss()，cancel()，stop()，Dialog都不会再执行onCreate()方法）。
2. show() 和 onStart()在每次Dialog显示时都会*依次*执行。
3. onDismiss() 和 stop() 在每次Dialog消失的时候都会*依次*执行。
4. cancel() 是在点击BACK按钮或者Dialog外部时触发，然后依次执行onDismiss() 和 stop()。

### 应用情景：

1. 点击显示按钮，第一次显示Dialog，然后按BACK键返回。 
   show() —> onCreate() —> onStart(); 
   cancel() —> onDismiss() —> Stop();
2. 再次点击显示按钮，然后点击Dialog外部。 
   show() —> onStart(); 
   cancel() —> onDismiss() —> Stop();
3. 再次点击显示按钮，然后执行Dialog.dismiss() 方法。 
   show() —> onStart(); 
   onDismiss() —> Stop();

## 问题解决

通过上面的描述，很容易看出来空值的原因。解决方案也很简单，只要把需要初始化的地方搬到构造方法中就可以解决。

