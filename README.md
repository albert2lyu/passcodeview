# PasscodeView
If you need a Passcode view (like PIN code or iOS Passcode) then there's a good widget. The basic
concept is a library that contains two seperated views.

#### 1.) PasscodeView
This is a full customizable numeric pad interface. Easy to use for lock screen, PIN pad, login or
other security reason. You can add new button or change the button style using PasscodeBaseAdapter.

#### 2.) PasscodeIndicator
An simple indicator view to feedback for user when type on the PasscodeView. You can place anywhere
in the layout XML independent PasscodeView. It is also highly customizable with XML attributes like
padding, margin, default or active drawable and of course animation.

### Screenshots
<img src="https://github.com/siczmj/passcodeview/blob/master/screenshots/passcode_view_example_plain.png" width="175" />
<img src="https://github.com/siczmj/passcodeview/blob/master/screenshots/passcode_view_example_customized.png" width="175" />
<img src="https://github.com/siczmj/passcodeview/blob/master/screenshots/passcode_view_example_ios.png" width="175" />
<img src="https://github.com/siczmj/passcodeview/blob/master/screenshots/passcode_view_example_android.png" width="175" />

### Demo
<img src="https://github.com/siczmj/passcodeview/blob/master/screenshots/passcode_view_android_capture.gif" width="250" height="403" />

------

## Usage

### Download

#### As library project

There is enough to download only the passcodeview-library which contains all neccessary files.

#### As Gradle dependency

The AAR file is available using by JitPack. So you need to edit the gradle files and run gradle sync.

Add it in your root build.gradle at the end of repositories:
```gradle
allprojects {
    repositories {
        ...
        maven { url "https://jitpack.io" }
    }
}
```

Add the dependency at the end of your /app/build.gradle files:
```gradle
dependencies {
        compile 'com.github.siczmj:passcodeview:v1.0.1'
}
```


### Add PasscodeView to your XML layout
```xml
<com.nirigo.mobile.view.passcode.PasscodeView
        android:id="@+id/passcode_view"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        />
```

#### Wrap mode or Stretch mode

The PasscodeView working as auto wrapping mode and stretch mode too. What does it mean? In wrap mode
the PasscodeView measure self by children sizes. It's good when you set the buttons size wrap_content
or any exact size. In this case you need to use appropriate *inflater* in getView() implementation.
See any [example of PasscodeViewAdapter](https://github.com/siczmj/passcodeview/tree/master/passcodeview-example/src/main/java/com/nirigo/mobile/passcode/examples/adapters).
If you set the layout_width and/or layout_height of PasscodeView to match_parent or exact size then
will be stretch each child with equal size. So, in this case not matter what is the size of the
children.

<img src="https://github.com/siczmj/passcodeview/blob/master/screenshots/passcode_view_wrap_mode.png" width="200" />
<img src="https://github.com/siczmj/passcodeview/blob/master/screenshots/passcode_view_stretch_mode.png" width="200" />


### Implement PasscodeBaseAdapter ...
```java
public class CustomPasscodeAdapter extends PasscodeBaseAdapter {

    private LayoutInflater inflater;

    public CustomPasscodeAdapter(Context context) {
        super(Arrays.asList(
                new PasscodeItem("1", PasscodeItem.TYPE_NUMBER),
                new PasscodeItem("2", PasscodeItem.TYPE_NUMBER),
                new PasscodeItem("3", PasscodeItem.TYPE_NUMBER),
                new PasscodeItem("4", PasscodeItem.TYPE_NUMBER),
                new PasscodeItem("5", PasscodeItem.TYPE_NUMBER),
                new PasscodeItem("6", PasscodeItem.TYPE_NUMBER),
                new PasscodeItem("7", PasscodeItem.TYPE_NUMBER),
                new PasscodeItem("8", PasscodeItem.TYPE_NUMBER),
                new PasscodeItem("9", PasscodeItem.TYPE_NUMBER),
                new PasscodeItem("X", PasscodeItem.TYPE_CLEAR),
                new PasscodeItem("0", PasscodeItem.TYPE_NUMBER),
                new PasscodeItem("<", PasscodeItem.TYPE_REMOVE)
        ));
        this.inflater = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {

        PasscodeItem item = getItem(position);

        if(convertView == null || convertView.getTag() != PasscodeItem.class){
            convertView = inflater.inflate(R.layout.button_passcode_custom, parent, false);
            convertView.setTag(PasscodeItem.class);
        }

        AppCompatButton button = (AppCompatButton) convertView;
        button.setText(item.getValue());
        button.setVisibility(item.getType() == PasscodeItem.TYPE_EMPTY ? View.INVISIBLE : View.VISIBLE);

        return convertView;

    }

}
```

#### ... then make an instance and set it!
```java
    CustomPasscodeAdapter adapter = new CustomPasscodeAdapter(getActivity());
    passcodeView.setAdapter(adapter);
```


### Add the PasscodeIndicator to your XML layout

Add the passcode xml namespace to access all custom attributes:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:passcode="http://schemas.android.com/apk/res-auto"
    ...
```

Add the indicator to xml and give the appearance parameters:

```xml
<com.nirigo.mobile.view.passcode.PasscodeIndicator
        android:id="@+id/passcode_indicator"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="24dp"
        android:layout_marginTop="24dp"

        passcode:indicator_background="@color/custom_gray"
        passcode:indicator_background_active="@color/custom_green"
        passcode:indicator_length="6"
        passcode:indicator_level="2"
        passcode:indicator_margin="7dp"
        passcode:indicator_size="12dp"
        passcode:indicator_animation="@anim/custom_indicator_animation"
        />
```

#### How to set the level in the indicator?

The indicator it's just a feedback for users so exactly not matter what indicate. That mean you
need to set the indicator level only and you want to use anything.

```java
    passcodeIndicator.setIndicatorLevel(yourCurrentPasscode.length());
```


#### Animate when something wrong

```java
    passcodeIndicator.wrongPasscode();
```

In this case the default animation will be shake all indicator drawables. See the [demo](https://github.com/siczmj/passcodeview#demo).


------

## Tips & Tricks

### How to add custom buttons and actions to PasscodeView?

It's really easy. You can see the example in the Android implementation:

1. You need to extends the [PasscodeItem](https://github.com/siczmj/passcodeview/blob/master/passcodeview-library/src/main/java/com/nirigo/mobile/view/passcode/models/PasscodeItem.java)
model and add the new parameters. Like [PasscodeItemAndroid](https://github.com/siczmj/passcodeview/blob/master/passcodeview-example/src/main/java/com/nirigo/mobile/passcode/examples/models/PasscodeItemAndroid.java) and [PasscodeItemAndroidImage](https://github.com/siczmj/passcodeview/blob/master/passcodeview-example/src/main/java/com/nirigo/mobile/passcode/examples/models/PasscodeItemAndroidImage.java)
in the models package of example project.

2. Make new adapter that use your unique PasscodeItems.
Like [AndroidPasscodeAdapter](https://github.com/siczmj/passcodeview/blob/master/passcodeview-example/src/main/java/com/nirigo/mobile/passcode/examples/adapters/AndroidPasscodeAdapter.java).

3. Handle it in the getView() implementation and pass your custom layout.
Like in the [AndroidPasscodeAdapter.getView()](https://github.com/siczmj/passcodeview/blob/master/passcodeview-example/src/main/java/com/nirigo/mobile/passcode/examples/adapters/AndroidPasscodeAdapter.java#L45) I added Enter event with ImageView

4. Catch the onItemClick and hanle the custom event.
like in the [ExampleAndroidFragment](https://github.com/siczmj/passcodeview/blob/master/passcodeview-example/src/main/java/com/nirigo/mobile/passcode/examples/ExampleAndroidFragment.java#L54), check Enter event.


### Can I use any drawable/mipmap/color on indicator?

Yes.

### What mean the type in the PasscodeItem?

Type member can help you seperate View type or action type.

| Field         | Value | Explain                               |
| --------------|:-----:| ------------------------------------- |
| TYPE_EMPTY    | -1    | Use if you want to hide an View.      |
| TYPE_NUMBER   |  0    | Typical mean dial number.             |
| TYPE_REMOVE   |  1    | Backspace or remove last character.   |
| TYPE_CLEAR    |  2    | Clear all text.                       |
| TYPE_ENTER    |  3    | Send previously typed characters.     |


------

## Samples

There are follow [samples](https://github.com/siczmj/passcodeview/tree/master/passcodeview-example/src/main/java/com/nirigo/mobile/passcode/examples) available:

1. Plain usage:

    - ExamplePlainFragment.java

2. Customization example:

    - ExampleCustomizedFragment.java
    - CustomPasscodeAdapter.java

3. IOS look example:

    - ExampleIOSFragment.java
    - PasscodeItemIOS.java
    - IOSPasscodeAdapter.java
    - ...and corresponding elements

4. Android PIN code example:

    - ExampleAndroidFragment.java
    - PasscodeItemAndroid.java and PasscodeItemAndroidImage.java
    - AndroidPasscodeAdapter.java
    - ...and corresponding elements

------

## TODO

- change number of column
- add selector support
- add watch() method to passcodeindicator to listening EditText/Autocomplete..
- example for imageview implementation
- AAR in maven repo


## License
See the LICENSE file in the project root.
