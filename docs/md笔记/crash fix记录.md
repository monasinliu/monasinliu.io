# Crash fix 记录

1. Activity Not Found(1)

```
android.content.ActivityNotFoundException: No Activity found to handle Intent { act=android.intent.action.VIEW dat=http://onelink.to/... } 	at android.app.Instrumentation.checkStartActivityResult(Instrumentation.java:1982) 	at android.app.Instrumentation.execStartActivity(Instrumentation.java:1637) 	at android.app.Activity.startActivityForResult(Activity.java:4711) 	at androidx.fragment.app.FragmentActivity.startActivityForResult(FragmentActivity.java:675) 	at android.app.Activity.startActivityForResult(Activity.java:4651) 	at androidx.fragment.app.FragmentActivity.startActivityForResult(FragmentActivity.java:662) 	at android.app.Activity.startActivity(Activity.java:5072) 	at android.app.Activity.startActivity(Activity.java:5040) 	at com.mydito.ui.load.PaymentMethodActivity$6.onRightBtnClick(PaymentMethodActivity.java:586) 	at com.mydito.ui.home.widgets.MsgConfirmDialog.lambda$onCreate$1$MsgConfirmDialog(MsgConfirmDialog.java:59) 	at com.mydito.ui.home.widgets.-$$Lambda$MsgConfirmDialog$bfXD_ioW_rzbF5LAqGmSi$
```

启动错误，搜索 `http://onelink.to/` 定位位置。

添加try catch：

```java
  try {
                    Intent intent = new Intent(Intent.ACTION_VIEW);
                    Uri uri = Uri.parse("http://onelink.to/9v3gtp");
                    intent.setData(uri);
                    activity.startActivity(intent);
                } catch (Exception e) {
                    LogUtils.e(e);
                    ToastUtils.showShort("Unknown Error,Please go to the app store to download it.");
                }
```

2. Null Pointer Exception

   ```
   java.lang.NullPointerException: Argument 'key' of type String (#0 out of 2, zero-based) is marked by @androidx.annotation.NonNull but got null for it 	at java.util.Objects.requireNonNull(Objects.java:228) 	at com.blankj.utilcode.util.SPUtils.getString(SPUtils.java:130) 	at com.iwhalecloud.basemoudle.utils.SPUtilsHelp.getDataList(SPUtilsHelp.java:107) 	at com.mydito.ui.home.util.MenuUtils.getDefaultFocus(MenuUtils.java:156) 	at com.mydito.ui.home.fragment.HomeLoggedInFragment.initMenuList(HomeLoggedInFragment.java:482) 	at com.mydito.ui.home.fragment.HomeLoggedInFragment.initView(HomeLoggedInFragment.java:187) 	at com.mydito.ui.home.fragment.HomeLoggedInFragment.onStart(HomeLoggedInFragment.java:165) 	at androidx.fragment.app.Fragment.performStart(Fragment.java:2730) 	at androidx.fragment.app.FragmentStateManager.start(FragmentStateManager.java:355) 	at androidx.fragment.app.FragmentManager.moveToState(FragmentManager.java:1192) 	at androidx.fragment.app.FragmentManager.moveToState(FragmentManager.java:1354) $
   ```

   报错位置

   ```java
   List<ServiceMenu> menus = SPUtilsHelp.getInstance().getDataList(DitoApplication.getMenuFocusKey());
   ```

   ```java
   public static String getMenuFocusKey() {
           if (userInfoResp() != null)
               return userInfoResp().getDitoNumber();
           return "";
       }
   ```

   spUtil

   ![image-20211018155633815](/Users/ruiyu/Library/Application Support/typora-user-images/image-20211018155633815.png)

   此处 `userInfoResp().getDitoNumber();` 可能为空，所以将getMenuFocusKey方法改为

   ```java
   public static String getMenuFocusKey() {
           if (userInfoResp() != null)
               return EmptyUtil.check(userInfoResp().getDitoNumber())? "":userInfoResp().getDitoNumber();;
           return "";
       }
   ```

   

   

3. 有疑问的：

   ```
   java.lang.RuntimeException: Unable to resume activity {com.mydito/com.mydito.ui.home.MainActivity}: java.lang.NullPointerException: Argument 'key' of type String (#0 out of 2, zero-based) is marked by @androidx.annotation.NonNull but got null for it 	at android.app.ActivityThread.performResumeActivity(ActivityThread.java:3853) 	at android.app.ActivityThread.handleResumeActivity(ActivityThread.java:3893) 	at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1815) 	at android.os.Handler.dispatchMessage(Handler.java:106) 	at android.os.Looper.loop(Looper.java:214) 	at android.app.ActivityThread.main(ActivityThread.java:6977) 	at java.lang.reflect.Method.invoke(Native Method) 	at com.android.internal.os.RuntimeInit$MethodAndArgsCaller.run(RuntimeInit.java:528) 	at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:910) Caused by: java.lang.NullPointerException: Argument 'key' of type String (#0 out of 2, zero-based) is marked by @androidx.annotation.NonNull but got null for it 	at java.util.Obj$
   ```

4. UCC

5. 5\6\7\8同3

6. 9

   ```
   android.content.ActivityNotFoundException: Unable to find explicit activity class {com.miui.phrase/com.miui.phrase.AddPhraseActivity}; have you declared this activity in your AndroidManifest.xml? 	at android.app.Instrumentation.checkStartActivityResult(Instrumentation.java:2064) 	at android.app.Instrumentation.execStartActivity(Instrumentation.java:1720) 	at android.app.Activity.startActivityForResult(Activity.java:5277) 	at androidx.fragment.app.FragmentActivity.startActivityForResult(FragmentActivity.java:675) 	at android.app.Activity.startActivityForResult(Activity.java:5235) 	at androidx.fragment.app.FragmentActivity.startActivityForResult(FragmentActivity.java:662) 	at android.app.Activity.startActivity(Activity.java:5606) 	at android.app.Activity.startActivity(Activity.java:5574) 	at android.widget.Editor$ActionPopupWindow.startAddPhraseActivity(Editor.java:4774) 	at android.widget.Editor$ActionPopupWindow.onClick(Editor.java:4978) 	at android.view.View.performClick(View.java:7307) 	at android.widget.T$
   ```

7. x 36

   ```
   android.os.BadParcelableException: ClassNotFoundException when unmarshalling: androidx.fragment.app.FragmentManagerState 	at android.os.Parcel.readParcelableCreator(Parcel.java:3050) 	at android.os.Parcel.readParcelable(Parcel.java:2967) 	at android.os.Parcel.readValue(Parcel.java:2869) 	at android.os.Parcel.readArrayMapInternal(Parcel.java:3252) 	at android.os.BaseBundle.initializeFromParcelLocked(BaseBundle.java:292) 	at android.os.BaseBundle.unparcel(BaseBundle.java:236) 	at android.os.BaseBundle.size(BaseBundle.java:364) 	at android.app.servertransaction.LaunchActivityItem.hashCode(LaunchActivityItem.java:208) 	at java.util.AbstractList.hashCode(AbstractList.java:541) 	at java.util.Objects.hashCode(Objects.java:98) 	at android.app.servertransaction.ClientTransaction.hashCode(ClientTransaction.java:241) 	at android.app.servertransaction.TransactionExecutorHelper.tId(TransactionExecutorHelper.java:266) 	at android.app.servertransaction.TransactionExecutor.execute(TransactionExecutor.java:86) 	at android.ap$
   ```

8. x

   ```
   
   
   ```

9. 12 13 14 15 UCC

10. 22图标过大RemoteServiceException

    ```
    android.app.RemoteServiceException: Attempt to invoke virtual method 'int com.android.server.wm.TaskDisplayArea.getDisplayId()' on a null object reference 	at android.app.ActivityThread$H.handleMessage(ActivityThread.java:2054) 	at android.os.Handler.dispatchMessage(Handler.java:106) 	at android.os.Looper.loop(Looper.java:250) 	at android.app.ActivityThread.main(ActivityThread.java:7755) 	at java.lang.reflect.Method.invoke(Native Method) 	at com.android.internal.os.RuntimeInit$MethodAndArgsCaller.run(RuntimeInit.java:592) 	at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:958) 
    ```

11. ActivityNotFoundException: `act=android.intent.action.PICK dat=content://com.android.contacts/contacts`

    LoadActivity 186 

    TransferDataFragment 117

    TransferLoadFragment 116

12. ActivityNotFoundException：` { act=android.intent.action.PICK dat=content://media/external/images/media typ=image/* (has extras) } `

    IJoinWebViewActivity 图片选择

13. ![image-20211018162609461](/Users/ruiyu/Library/Application Support/typora-user-images/image-20211018162609461.png)

IjoinWebViewActiivty openLink方法

14. ![image-20211018162700855](/Users/ruiyu/Library/Application Support/typora-user-images/image-20211018162700855.png)

    同上，查看

15. 37-41 ArrayIndexOutOfBoundsException

    ```
    java.lang.ArrayIndexOutOfBoundsException: Out of range  in /Users/cm/Realm/realm-java/realm/realm-library/src/main/cpp/io_realm_internal_OsResults.cpp line 98(requested: 9 valid: 9) 	at io.realm.internal.OsResults.nativeGetRow(Native Method) 	at io.realm.internal.OsResults.getUncheckedRow(OsResults.java:345) 	at io.realm.OrderedRealmCollectionImpl.get(OrderedRealmCollectionImpl.java:129) 	at io.realm.RealmResults.get(RealmResults.java:69) 	at com.ucc.chat.adapter.ChatAdapter.getItemViewType(ChatAdapter.java:185) 	at android.widget.AbsListView.obtainView(AbsListView.java:3159) 	at android.widget.ListView.makeAndAddView(ListView.java:2197) 	at android.widget.ListView.fillDown(ListView.java:824) 	at android.widget.ListView.fillGap(ListView.java:788) 	at android.widget.AbsListView.trackMotionScroll(AbsListView.java:8183) 	at android.widget.ListView.trackMotionScroll(ListView.java:2116) 	at android.widget.AbsListView$FlingRunnable.run(AbsListView.java:7606) 	at android.view.Choreographer$CallbackRecord.run(Choreo$
    ```

    ChatAdapter

16. IllegalArgumentException 42

```
java.lang.IllegalArgumentException 	at com.android.internal.util.Preconditions.checkArgument(Preconditions.java:33) 	at android.widget.SelectionActionModeHelper$TextClassificationHelper.init(SelectionActionModeHelper.java:641) 	at android.widget.SelectionActionModeHelper.resetTextClassificationHelper(SelectionActionModeHelper.java:204) 	at android.widget.SelectionActionModeHelper.invalidateActionModeAsync(SelectionActionModeHelper.java:105) 	at android.widget.Editor.invalidateActionModeAsync(Editor.java:2067) 	at android.widget.Editor.showFloatingToolbar(Editor.java:1458) 	at android.widget.Editor.updateFloatingToolbarVisibility(Editor.java:1436) 	at android.widget.Editor.onTouchEvent(Editor.java:1402) 	at android.widget.TextView.onTouchEvent(TextView.java:9805) 	at android.view.View.dispatchTouchEvent(View.java:11969) 	at android.view.ViewGroup.dispatchTransformedTouchEvent(ViewGroup.java:3052) 	at android.view.ViewGroup.dispatchTouchEvent(ViewGroup.java:2703) 	at android.view.ViewGroup.dispatchTransformedT$
```

17. 