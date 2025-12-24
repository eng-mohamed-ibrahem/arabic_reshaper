# arabic_reshaper

A lightweight Dart/Flutter package for reshaping Arabic text. This package is essential for environments that do not support Arabic script rendering natively (such as specific PDF generation libraries, image editors, or 3D engines).

It converts disjointed Arabic characters into their connected forms, handles bidirectional reshaping (visual), and manages complex ligatures.

## Features

- **Grammatical Shaping:** Converts isolated letters to their correct Initial, Medial, Final, or Isolated forms.
- **Ligature Support:** Automatically merges characters into ligatures (e.g., Lam-Alef `لا`, Allah `ﷲ`).
- **Diacritics (Harakat):** Option to keep or remove Tashkeel.
- **Tatweel (Kashida):** Option to keep or remove elongation characters.
- **Customizable:** extensive configuration options via `ArabicReshaperConfig`.

## Installation

Add the package to your `pubspec.yaml`:

```yaml
dependencies:
  arabic_reshaper: ^1.0.0
```

Then import it:

```dart
import 'package:arabic_reshaper/arabic_reshaper.dart';
```

## Usage

### 1. Basic Usage (Singleton)

For standard text reshaping using default settings:

```dart
String text = "السلام عليكم";
String reshaped = ArabicReshaper.instance.reshape(text);
print(reshaped);
// Before reshaping view env: "ﻢﻜﻴﻠﻋ ﻡﻼﺴﻟﺍ"
// After reshaping view env: "السلام عليكم"
```

### 2. Custom Configuration

You can customize the reshaper behavior by passing a `ArabicReshaperConfig` object.

```dart
final reshaper = ArabicReshaper(
  configuration: ArabicReshaperConfig(
    deleteHarakat: true,
    deleteTatweel: true,
    supportLigatures: true,
  ),
);

String reshaped = reshaper.reshape("الْعَرَبيَّةُ");
// Output reshaped view env: ﺍﻟﻌﺮﺑﻴﺔ
```

## Examples & Configuration

Here is a breakdown of how the reshaper handles different scenarios based on the configuration.

### Basic & Mixed Text

Reshapes Arabic while leaving non-Arabic characters untouched.

| Feature   | Input            | Output           |
| :-------- | :--------------- | :--------------- |
| **Basic** | `"السلام عليكم"` | `"ﺍﻟﺴﻼﻡ ﻋﻠﻴﻜﻢ"`  |
| **Mixed** | `"Hello العالم"` | `"Hello ﺍﻟﻌﺎﻟﻢ"` |

### Handling Harakat (Tashkeel)

Control whether diacritics are preserved or removed.

```dart
// config: deleteHarakat: true (Default)
reshaper.reshape("الْعَرَبيَّةُ"); // Output: "ﺍﻟﻌﺮﺑﻴﺔ"

// config: deleteHarakat: false
reshaper.reshape("الْعَرَبيَّةُ"); // Output: "ﺍﻟْﻌَﺮَﺑﻴَّﺔُ"
```

### Handling Tatweel (Elongation)

Control whether the Kashida character (`_`) is preserved.

```dart
// config: deleteTatweel: false (Default)
reshaper.reshape("الـــعـــربـــيـــة"); // Output: "ﺍﻟـــﻌـــﺮﺑـــﻴـــﺔ"

// config: deleteTatweel: true
reshaper.reshape("الـــعـــربـــيـــة"); // Output: "ﺍﻟﻌﺮﺑﻴﺔ"
```

### Ligatures

The package supports standard ligatures (like Lam-Alef) and special religious ligatures.

| Ligature Type | Input       | Output    | Note                         |
| :------------ | :---------- | :-------- | :--------------------------- |
| **Lam-Alef**  | `"الاسلام"` | `"ﺍﻻﺳﻼﻡ"` | Standard shaping             |
| **Allah**     | `"الله"`    | `"ﷲ"`     | Special Glyph                |
| **Disabled**  | `"الله"`    | `"ﺍﻟﻠﻪ"`  | If `supportLigatures: false` |

### Specific Ligature Overrides

You can enable specific complex ligatures that are disabled by default (or vice versa) using `ligatureOverrides`.

```dart
final reshaper = ArabicReshaper(
  configuration: ArabicReshaperConfig(
  ligatureOverrides: {
    'ARABIC LIGATURE SALLALLAHOU ALAYHE WASALLAM': true
    },
  ),
);
final reshaped = reshaper.reshape("صلى الله عليه وسلم");
print(reshaped); // Output: "ﷺ"
```

## Utility Methods

### Check for Arabic Content

Check if a string contains any Arabic characters (Standard or Extended).

```dart
bool isArabic = ArabicReshaper.isArabic("مرحبا"); // true
bool isEnglish = ArabicReshaper.isArabic("Hello"); // false
```

## Configuration Reference (`ArabicReshaperConfig`)

| Property                       | Type                | Default    | Description                                                   |
| :----------------------------- | :------------------ | :--------- | :------------------------------------------------------------ |
| `language`                     | `String`            | `'Arabic'` | Target language.                                              |
| `deleteHarakat`                | `bool`              | `true`     | Removes Tashkeel/Diacritics.                                  |
| `shiftHarakatPosition`         | `bool`              | `false`    | Shifts Harakat visually for certain fonts.                    |
| `deleteTatweel`                | `bool`              | `false`    | Removes the `_` character.                                    |
| `supportZwj`                   | `bool`              | `true`     | Supports Zero Width Joiner.                                   |
| `supportLigatures`             | `bool`              | `true`     | Enables complex character merging.                            |
| `useUnshapedInsteadOfIsolated` | `bool`              | `false`    | Use raw character instead of Isolated form if not connecting. |
| `ligatureOverrides`            | `Map<String, bool>` | `{}`       | Force enable/disable specific ligatures.                      |

---

This package is a Dart implementation inspired by [python-arabic-reshaper](https://github.com/mpcabd/python-arabic-reshaper/)
