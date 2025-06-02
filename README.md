# Emoji

A Kotlin Multiplatform library to add Emoji support to your Android App / JVM Backend.

- JVM
  - Check out the sample jvm module for text parsing / searching functionality
- Android
  - Picking
    - [`EmojiPopup`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/EmojiPopup.kt) - [PopupWindow](https://developer.android.com/reference/android/widget/PopupWindow) which overalys over the soft keyboard
    - [`EmojiView`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/EmojiView.kt) - Normal view which is used by `EmojiPopup` and can also be used as a standalone to select emojis via categories
  - Displaying (Android)
    - [`EmojiAutoCompleteTextView`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/EmojiAutoCompleteTextView.kt)
    - [`EmojiButton`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/EmojiButton.kt)
    - [`EmojiCheckbox`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/EmojiCheckbox.kt)
    - [`EmojiEditText`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/EmojiEditText.kt)
    - [`EmojiMultiAutoCompleteTextView`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/EmojiMultiAutoCompleteTextView.kt)
    - [`EmojiTextView`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/EmojiTextView.kt)
    - For convenience, there's also a [`EmojiLayoutFactory`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/EmojiLayoutFactory.kt), which can be used to get automatic Emoji support when using normal Android Views such as `TextView`, `Checkbox`, etc.

The library has 4 different sprites providers to choose from ([iOS](#ios-emojis), [Google](#google), [Facebook](#facebook) & [Twitter](#twitter)). The emoji's are packaged as pictures and loaded at runtime. If you want to use a Font provider, check out [Google Compat](#google-compat). Alternatively, we also offer [AndroidX Emoji2 support](#androidx-emoji2).

## iOS Emojis

<img src="./fastlane/metadata/android/en-US/images/phoneScreenshots/ios_1.png" alt="Normal Keyboard" width="270"><img src="./fastlane/metadata/android/en-US/images/phoneScreenshots/ios_2.png" alt="Emoji Keyboard" width="270" hspace="20"><img src="./fastlane/metadata/android/en-US/images/phoneScreenshots/ios_3.png" alt="Recent Emojis" width="270">

For getting the above iOS Emojis, add the dependency:

```groovy
implementation("com.vanniktech:emoji-ios:0.21.0")
```

And install the provider in your Application class.

```kotlin
import com.vanniktech.emoji.EmojiManager
import com.vanniktech.emoji.ios.IosEmojiProvider

EmojiManager.install(IosEmojiProvider())
```

## Custom Emojis

If you want to display your own Emojis you can create your own implementation of [`EmojiProvider`](./emoji/src/commonMain/kotlin/com/vanniktech/emoji/EmojiProvider.kt) and pass it to `EmojiManager.install`.

All of the core API lays in `emoji`, which is being pulled in automatically by the providers:

```groovy
implementation("com.vanniktech:emoji:0.21.0")
```

## Android Material

Material Design Library bindings can be included via:

```groovy
implementation("com.vanniktech:emoji-material:0.21.0")
```

- [`EmojiMaterialButton`](./emoji-material/src/androidMain/kotlin/com/vanniktech/emoji/material/mojiMaterialButton.kt)
- [`EmojiMaterialRadioButton`](./emoji-material/src/androidMain/kotlin/com/vanniktech/emoji/material/EmojiMaterialRadioButton.kt)
- [`EmojiMaterialCheckBox`](./emoji-material/src/androidMain/kotlin/com/vanniktech/emoji/material/MaterialCheckBox.kt)
- [`EmojiTextInputEditText`](./emoji-material/src/androidMain/kotlin/com/vanniktech/emoji/material/EmojiTextInputEditText.kt)

For convenience, there's also a [`MaterialEmojiLayoutFactory`](./emoji-material/src/androidMain/kotlin/com/vanniktech/emoji/material/MaterialEmojiLayoutFactory.kt), which can be used to get automatic Emoji support.

## Set up Android

### Inserting Emojis

Declare your [`EmojiEditText`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/EmojiEditText.kt) in your layout xml file.

```xml
<com.vanniktech.emoji.EmojiEditText
  android:id="@+id/emojiEditText"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:imeOptions="actionSend"
  android:inputType="textCapSentences|textMultiLine"
  android:maxLines="3"/>
```

To open the [`EmojiPopup`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/EmojiPopup.kt) execute the code below:

```kotlin
val emojiPopup = EmojiPopup(rootView, emojiEditText)
emojiPopup.toggle() // Toggles visibility of the Popup.
emojiPopup.dismiss() // Dismisses the Popup.
emojiPopup.isShowing() // Returns true when Popup is showing.
```

The `rootView` is the rootView of your layout xml file which will be used for calculating the height of the keyboard.
`emojiEditText` is the [`EmojiEditText`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/EmojiEditText.kt) that you declared in your layout xml file.

### Displaying Emojis

```xml
<com.vanniktech.emoji.EmojiTextView
  android:id="@+id/emojiTextView"
  android:layout_width="wrap_content"
  android:layout_height="wrap_content"/>
```

Just use the [`EmojiTextView`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/EmojiTextView.kt) and call `setText` with the String that contains Unicode encoded Emojis. To change the size of the displayed Emojis call one of the `setEmojiSize` methods.

### EmojiPopup Listeners

The [`EmojiPopup`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/EmojiPopup.kt) class allows you to declare several listeners.

```kotlin
EmojiPopup(
  onSoftKeyboardCloseListener = { },
  onEmojiClickListener = { },
  onSoftKeyboardOpenListener = { },
  onEmojiPopupShownListener = { },
  onEmojiPopupDismissListener = { },
  onEmojiBackspaceClickListener = { },
)
```

### EmojiPopup Configuration

#### Custom Recent Emoji implementation

You can pass your own implementation of the recent Emojis. Implement the [`RecentEmoji`](./emoji/src/commonMain/kotlin/com/vanniktech/emoji/recent/RecentEmoji.kt) interface and pass it when you're building the [`EmojiPopup`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/EmojiPopup.kt):

```kotlin
EmojiPopup(
  recentEmoji = yourClassThatImplementsRecentEmoji,
)
```

If no instance or a null instance is set the [default implementation](emoji/src/main/java/com/vanniktech/emoji/RecentEmojiManager.kt) will be used.

#### Custom Variant Emoji implementation

You can pass your own implementation of the variant Emojis. Implement the [`VariantEmoji`](./emoji/src/commonMain/kotlin/com/vanniktech/emoji/variant/VariantEmoji.kt) interface and pass it when you're building the [`EmojiPopup`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/EmojiPopup.kt):

```kotlin
EmojiPopup(
  variantEmoji = yourClassThatImplementsVariantEmoji,
)
```

If no instance or a null instance is set the [default implementation](emoji/src/main/java/com/vanniktech/emoji/VariantEmojiManager.kt) will be used.

#### Custom Search Emoji implementation

You can pass your own implementation for searching Emojis. Implement the [`SearchEmoji`](./emoji/src/commonMain/kotlin/com/vanniktech/emoji/search/SearchEmoji.kt) interface and pass it when you're building the [`EmojiPopup`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/EmojiPopup.kt):

```kotlin
EmojiPopup(
  searchEmoji = yourClassThatImplementsSearchEmoji,
)
```

If no instance or a null instance is set the [default implementation](./emoji/src/commonMain/kotlin/com/vanniktech/emoji/search/SearchEmojiManager.kt) will be used.

### Animations

#### Custom keyboard enter and exit animations

You can pass your own animation style for enter and exit transitions of the Emoji keyboard while you're building the [`EmojiPopup`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/EmojiPopup.kt):

```kotlin
EmojiPopup(
  keyboardAnimationStyle = emoji_fade_animation_style,
)
```

If no style is set the keyboard will appear and exit as a regular PopupWindow.
This library currently ships with two animation styles as an example:

- R.style.emoji_slide_animation_style
- R.style.emoji_fade_animation_style

#### Custom page transformers

You can pass your own Page Transformer for the Emoji keyboard View Pager while you're building the [`EmojiPopup`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/EmojiPopup.kt):

```kotlin
EmojiPopup(
  pageTransformer = MagicTransformer(),
)
```

If no transformer is set ViewPager will behave as its usual self. Please do note that this library currently does not ship any example Page Transformers.

### Other goodies
- [`MaximalNumberOfEmojisInputFilter`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/inputfilters/MaximalNumberOfEmojisInputFilter.kt) can be used to limit the number of Emojis one can type into an EditText
- [`OnlyEmojisInputFilter`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/inputfilters/OnlyEmojisInputFilter.kt) can be used to limit the input of an EditText to emoji only
- [`ForceSingleEmojiTrait`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/traits/ForceSingleEmojiTrait.kt) can be used to force a single emoji which can be replaced by a new one
- [`DisableKeyboardInputTrait`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/traits/DisableKeyboardInputTrait.kt) disable input of the normal soft keyboard
- [`SearchInPlaceTrait`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/traits/SearchInPlaceTrait.kt) search for an emoji using :query
- [`EmojiView`](./emoji/src/androidMain/kotlin/com/vanniktech/emoji/EmojiView.kt) View of all emojis and categories which does not depend on a keyboard
- `EmojiEditText#disableKeyboardInput()` to disable normal keyboard input. To undo call `#enableKeyboardInput()`

Most of them are also showcased in the sample app.

## Set up JVM

Install one of the providers you want, for instance the iOS Provider:

```kotlin
import com.vanniktech.emoji.EmojiManager
import com.vanniktech.emoji.ios.IosEmojiProvider

EmojiManager.install(IosEmojiProvider())
```

Now you can use all the API's you want, for instance when you want to search for `swim` emojis, you can do:

```kotlin
import com.vanniktech.emoji.search.SearchEmojiManager

SearchEmojiManager().search(query = "swim")
  .forEach {
    println(it)
  }
```

# Proguard / R8

No configuration needed.
