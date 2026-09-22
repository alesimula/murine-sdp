[![](https://jitpack.io/v/alesimula/murine-sdp.svg)](https://jitpack.io/#alesimula/murine-sdp) [![Build Status](https://img.shields.io/badge/build-passing-blue.svg)](https://jitpack.io/#alesimula/murine-sdp) [![License](https://img.shields.io/github/license/alesimula/murine-sdp)](https://github.com/alesimula/murine-sdp/blob/master/LICENSE)

# SDP - a scalable size unit
An android lib that provides a new size unit - sdp (scalable dp). This size unit scales with the screen size. It can help Android developers with supporting multiple screens.

A fork of [intuit/sdp](https://github.com/intuit/sdp) with the same values, packed so that it costs far less space in the app that uses it (especially on light usage). See [Differences from upstream](#differences-from-upstream).

for text views please refer to [ssp](https://github.com/intuit/ssp) which is based on the sp size unit for texts. 

# Attention
Use it carefully! for example, in most cases you still need to design a different layout for tablets.

# Example
Here is a layout built using sdp:

![sdp example](sdp_example.png)

And here is the same layout built using dp:

![dp example](dp_example.png)

You can see that sdp scales with the screen size and the dp stays with the same size on all screen sizes.

# Installation

Add the JitPack repository to your root `build.gradle` (or `settings.gradle`):

```gradle
allprojects {
    repositories {
        maven { url 'https://jitpack.io' }
    }
}
```

Then add the dependency:

```gradle
    implementation 'com.github.alesimula:murine-sdp:1.1.3'
```

# Getting Started

Reference the values by name. Note the `@fraction/` prefix and the zero padding, both of which differ from upstream:

```xml
<TextView
    android:layout_width="match_parent"
    android:layout_height="@fraction/_042sdp"
    android:paddingStart="@fraction/_010sdp" />
```

From code: `resources.getDimensionPixelSize(R.fraction._042sdp)`.

Aliasing one into a `<dimen>` trips lint's `ReferenceType` check. The value really is a dimension, so the warning is a false positive:

```xml
<resources xmlns:tools="http://schemas.android.com/tools">
    <dimen name="search_bar_height" tools:ignore="ReferenceType">@fraction/_042sdp</dimen>
</resources>
```

Referencing `@fraction/...` straight from a layout attribute does not trip it.

For easy mapping of designs to sdp units, one can create designs with 300 pixels screen width - in this case each pixel in the design corresponds to 1 sdp.

# Differences from upstream

- Values sit on the `fraction` resource type instead of `dimen`. They are still dimensions (`format="dimension"`); `fraction` is simply a type nothing else tends to use. Every config bucket in `resources.arsc` carries an offset array sized by its type's entry count, so upstream's 28 buckets made every `dimen` in the app pay for all 660 sdp values. In one real app this was 288 KB, against 52 KB here.
- Names are zero-padded (`_042sdp`, not `_42sdp`). Ids are assigned in sorted name order and the shrinker cuts a type's offset arrays at the highest surviving id, so padding lets the table stop just past the largest value the app uses. 52 KB down to 10 KB in that same app.
- Negatives (`_minus001sdp`) sort after every positive, so referencing one extends the table past all of them.
- `src/main/res/values*/` is generated. Change `unit`, `positiveMax` or `negativeMax` in the `createSDP` task in `sdp-android/build.gradle` and run it.


# Note
The sdp size unit calculation includes some approximation due to some performance and usability constraints.
