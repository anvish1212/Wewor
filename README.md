# Daily Basket: Architecture and Beginner's Guide

This guide explains what was built, how it works, which changes were made, and
why. Read it in order if you are new to Dart and Flutter.

## 1. What this project is

Daily Basket is a Blinkit-inspired Flutter grocery-shopping demo with its own
branding. It is not affiliated with Blinkit and does not use its APIs, logos,
or product photos.

Implemented features:

- Browsing 18 local demo products.
- Six product categories and an All option.
- Case-insensitive, multi-word search.
- Product details, prices, discounts, and storage information.
- Adding, increasing, decreasing, and removing cart items.
- Live item counts, subtotal, savings, and delivery charges.
- Responsive phone and desktop layouts.
- Empty-cart and empty-search states.
- An explicitly labelled demo checkout dialog.

Not implemented:

- A backend, database, or remote product API.
- Authentication or user accounts.
- Real inventory, address selection, payments, orders, or delivery tracking.
- Persisting the cart across application restarts.

Delivery times and the Bengaluru address are demo content, not live services.

### Feature-to-code navigation

Use this table to find the implementation of each feature. Start with the UI
component, then follow the data or logic link to understand its behavior.
Links are relative to this guide; `#L` anchors identify the current source line.
Line numbers can change when the source is edited, so the component names are
also included.

| Feature | UI component / entry point | Data or behavior |
|---|---|---|
| App startup and shared styling | [`GroceryApp`](lib/main.dart#L14) | [`main()` entry point](lib/main.dart#L6) and theme inside `GroceryApp`. |
| Storefront and product browsing | [`StorePage`](lib/main.dart#L47), [product grid](lib/main.dart#L304), and [`ProductCard`](lib/main.dart#L663) | [Local product catalog](lib/catalog.dart#L51) and [`Product` model](lib/catalog.dart#L1). |
| Category filtering | [Category selector and tap handler](lib/main.dart#L175) | [Category definitions](lib/catalog.dart#L31) and [`searchProducts()` category condition](lib/catalog.dart#L41). |
| Product search | [Search input and `onChanged`](lib/main.dart#L433) and [visible-results calculation](lib/main.dart#L90) | [`searchProducts()`](lib/catalog.dart#L41) normalizes text and matches all query words. |
| Clear search / reset filters | [Search field clear action](lib/main.dart#L433) and [`_emptySearch()` reset action](lib/main.dart#L611) | [Storefront state](lib/main.dart#L54) holds the search controller and selected category. |
| Product details | [`openProduct()`](lib/main.dart#L77) opens [`ProductSheet`](lib/main.dart#L871) | [`Product`](lib/catalog.dart#L1) provides descriptions, pack size, prices, and storage guidance. |
| Product illustrations | [`ProductArt`](lib/main.dart#L641) | [Catalog entries](lib/catalog.dart#L51) supply emoji and background colors. |
| Discount labels | [`ProductCard`](lib/main.dart#L663) and [`ProductSheet`](lib/main.dart#L871) | [`Product.discount` getter](lib/catalog.dart#L28). |
| Add an item | [`QuantityControl`](lib/main.dart#L787) | [`ShoppingCart.add()`](lib/shopping.dart#L29). |
| Increase quantity | [`QuantityControl`](lib/main.dart#L787) | [`ShoppingCart.add()`](lib/shopping.dart#L29) increments an existing entry. |
| Decrease quantity / remove item | [`QuantityControl`](lib/main.dart#L787) | [`ShoppingCart.remove()`](lib/shopping.dart#L41) decrements or removes an entry. |
| Shared, live cart updates | [Storefront listener](lib/main.dart#L91), [`QuantityControl`](lib/main.dart#L787), and [`CartSheet`](lib/main.dart#L982) | [`ShoppingCart`](lib/shopping.dart#L7) uses `ChangeNotifier` and calls `notifyListeners()` after changes. |
| Cart access and item count | [Bottom View cart button](lib/main.dart#L93), [`_header()` cart button](lib/main.dart#L360), and [`openCart()`](lib/main.dart#L66) | [`ShoppingCart.itemCount`](lib/shopping.dart#L20). |
| Basket contents and line prices | [`CartSheet`](lib/main.dart#L982) | [Cart product lookup and quantities](lib/shopping.dart#L11); each line displays price multiplied by quantity. |
| Subtotal and savings | [`CartSheet` bill details](lib/main.dart#L1115) and [`_billRow()`](lib/main.dart#L1197) | [`subtotal`](lib/shopping.dart#L21) and [`savings`](lib/shopping.dart#L22) getters. |
| Delivery charge and free-delivery threshold | [`CartSheet`](lib/main.dart#L982) | [Delivery constants](lib/shopping.dart#L8) and [`deliveryFee`](lib/shopping.dart#L24). |
| Grand total | [`CartSheet`](lib/main.dart#L982) | [`ShoppingCart.total`](lib/shopping.dart#L27). |
| Empty basket | [`CartSheet` empty-state branch](lib/main.dart#L1016) | [`itemCount`](lib/shopping.dart#L20) determines whether the empty state is shown. |
| No search results | [`_emptySearch()`](lib/main.dart#L611) | [`searchProducts()`](lib/catalog.dart#L41) returns an empty list when nothing matches. |
| Demo checkout | [Continue button and dialog](lib/main.dart#L1171) | Only displays a message; no order or payment operation exists. |
| Demo delivery location | [`_header()`](lib/main.dart#L360) and [`CartSheet`](lib/main.dart#L982) | Static display text, not GPS or an address service. |
| Promotional banner / fresh-picks shortcut | [`_hero()`](lib/main.dart#L519) | Its button selects Fruits & vegetables and clears the search. |
| Responsive header | [`_header()` layout breakpoint](lib/main.dart#L373) | Switches layout below 760 logical pixels of available width. |
| Responsive product grid | [Grid `LayoutBuilder`](lib/main.dart#L304) | [Column calculation](lib/main.dart#L306) limits the grid to two through six columns. |
| Cleanup of owned resources | [`_StorePageState.dispose()`](lib/main.dart#L60) | Disposes the shared cart and search controller when the storefront state is removed. |

### Feature-to-test navigation

| Feature coverage | Test |
|---|---|
| Search normalization, brand matching, and category restrictions | [Search unit test](test/widget_test.dart#L9). |
| Quantity changes, totals, savings, empty cart, and exact free-delivery threshold | [Cart unit test](test/widget_test.dart#L17). |
| Search, details, adding, cart updates, demo checkout, and removal | [Shopping-flow widget test](test/widget_test.dart#L64). |
| Empty results and resetting filters | [Search-reset widget test](test/widget_test.dart#L112). |
| Mobile category selection, empty basket, and adding | [Mobile-shopping widget test](test/widget_test.dart#L127). |
| Narrow-phone layout | [Narrow-layout widget test](test/widget_test.dart#L147). |

### Platform and configuration links

| Area | Source |
|---|---|
| Dependencies and application version | [pubspec.yaml](pubspec.yaml). |
| Android application ID, SDK settings, and signing configuration | [Android build configuration](android/app/build.gradle.kts). |
| Android display name and activity declaration | [Android manifest](android/app/src/main/AndroidManifest.xml). |
| iOS display name and platform settings | [iOS Info.plist](ios/Runner/Info.plist). |
| Browser title and metadata | [Web entry page](web/index.html). |
| Web app name and theme metadata | [Web manifest](web/manifest.json). |
| Editor Flutter SDK location | [VS Code settings](.vscode/settings.json). |
| Run, build, and installation instructions | [README.md](README.md). |

## 2. Flutter versus Dart

**Dart** is the programming language. It describes data and behavior:

```dart
final price = 28;
final quantity = 2;
final total = price * quantity;
```

**Flutter** is the framework used to build the interface:

```dart
Text('Daily Basket')
```

```dart
FilledButton(
  onPressed: () {},
  child: const Text('Add to cart'),
)
```

Flutter interface building blocks are called **widgets**. Widgets describe
layout, styling, text, controls, and interaction.

One Flutter application can target multiple platforms, but each platform needs
its own build environment. Android requires Android build tools; native iOS
builds require macOS and Xcode.

## 3. Architecture at a glance

The application uses a small separation of responsibilities, not a full
enterprise architecture or a formal MVVM/Clean Architecture implementation.

```text
User interaction
       |
       v
UI / presentation
Storefront, product cards, product sheet, cart sheet
       |
       +--------------------------+
       |                          |
       v                          v
Catalog and search           Shopping cart
Product definitions          Quantities and calculations
       |                          |
       +--------------------------+
                    |
                    v
             In-memory data
        No backend or database yet
```

The main responsibilities are:

| File | Responsibility |
|---|---|
| [lib/main.dart](lib/main.dart) | Application entry point, theme, storefront, reusable widgets, and shopping sheets. |
| [lib/catalog.dart](lib/catalog.dart) | Product model, category definitions, local product data, and search. |
| [lib/shopping.dart](lib/shopping.dart) | Cart quantities, price calculations, and change notifications. |
| [test/widget_test.dart](test/widget_test.dart) | Unit and widget tests. |

This is **separation of concerns**: each part has a clear job. Changing a delivery
rule should not require changing product-card layout.

## 4. Project structure

| Location | Purpose |
|---|---|
| [lib/](lib/) | Dart application source. |
| [test/](test/) | Automated tests. |
| [android/](android/) | Android host project and build configuration. |
| [ios/](ios/) | iOS host project and configuration. |
| [web/](web/) | Web entry page, metadata, manifest, and icons. |
| [.vscode/](.vscode/) | Workspace editor configuration. |
| [pubspec.yaml](pubspec.yaml) | Package name, dependencies, SDK constraint, version, and asset configuration. |
| [pubspec.lock](pubspec.lock) | Resolved dependency versions. |
| [analysis_options.yaml](analysis_options.yaml) | Static-analysis and lint configuration. |
| [README.md](README.md) | Setup, features, limitations, and run instructions. |

The generated `build` directory contains outputs such as the web application
and Android APK. Do not manually edit generated outputs: rebuilding can replace
them.

## 5. Application startup

The entry point in [lib/main.dart](lib/main.dart) is:

```dart
void main() => runApp(const GroceryApp());
```

The sequence is:

1. Dart calls `main()`.
2. `runApp()` starts Flutter with the supplied root widget.
3. `GroceryApp` returns a `MaterialApp`.
4. `MaterialApp` configures the application and displays `StorePage`.

`MaterialApp` provides Material Design integration, navigation support, a theme,
and the initial screen.

The shared theme defines green action colors, text styling, button padding,
and rounded corners. Central styling keeps controls consistent.

### Common Dart syntax

| Syntax | Meaning |
|---|---|
| `final` | A variable or field can be assigned only once. The referenced object can still be mutable. |
| `const` | A compile-time constant when used with constant inputs. |
| `required` | The caller must supply this named argument. |
| `=>` | Short syntax for a function returning one expression. |
| `_name` | A name private to its Dart library. |
| `String` / `int` / `bool` | Text, integer, and boolean types. |
| `?` after a type | The value may be null. |
| `??` | Use the right-hand value when the left-hand value is null. |
| `!` after an expression | Assert that a nullable value is not null; incorrect assumptions cause runtime errors. |

## 6. Product data

The `Product` class in [lib/catalog.dart](lib/catalog.dart) is the blueprint for
one product.

| Field | Purpose |
|---|---|
| `id` | Stable identifier such as `milk`. |
| `name` | Display name. |
| `brand` | Demo brand name. |
| `category` | Category used by filters. |
| `unit` | Pack size such as `500 ml`. |
| `price` | Selling price in whole rupees. |
| `mrp` | List price in whole rupees. |
| `art` | Emoji illustration. |
| `background` | Illustration background color encoded as an integer. |
| `description` | Product description. |
| `storage` | Storage guidance. |

The fields are final and the constructor is const, allowing immutable product
objects and a constant demo catalog.

A stable ID is preferable to using the name as identity: names may change while
the underlying product remains the same.

### Computed discount

```dart
int get discount => ((mrp - price) * 100 / mrp).round();
```

For a product with MRP 32 and selling price 28:

```text
Discount = (32 - 28) / 32 * 100
         = 12.5 percent
Displayed discount = 13 percent
```

A **getter** computes a value accessed like a property. The discount is derived
instead of separately stored, avoiding inconsistent price and discount data.

This formula assumes a valid positive MRP, as supplied by the local catalog.
External data would need validation.

### Why local products?

The catalog is a constant list compiled into the app. This allows us to build
the shopping flow without first building a server, database, or network layer.
Updating these products currently requires changing code and rebuilding.

Prices use integer rupees because the demo has no fractional prices. A real
commerce system commonly represents money as integer minor units, such as
paise, and verifies all charges on the server.

## 7. Search and category filtering

The `searchProducts(query, category)` function:

1. Trims leading and trailing spaces.
2. Converts the query to lowercase.
3. Splits it into words using whitespace.
4. Combines each product's name, brand, and category into searchable text.
5. Includes products matching the category and every search word.

```dart
final terms = query.trim().toLowerCase().split(RegExp(r'\s+'));
```

Example:

```text
Input: "  MORNING   FARM  "
Terms: ["morning", "farm"]
```

The condition `terms.every(text.contains)` requires every term to occur
somewhere in the searchable text. It is not fuzzy search and does not require
an exact adjacent phrase.

Search and category filtering are combined:

```text
Search "milk" + category "Snacks" = no matches
Search "milk" + category "All" = milk product
```

An empty query matches all products in the selected category. The reset action
clears both the query and category.

For 18 local products, immediate filtering is inexpensive. A large remote
catalog would normally require an API, pagination, and possibly debouncing.

## 8. State management

**State** means information that can change while the application is running.

There are two kinds in this project:

| State | Owner | Update mechanism |
|---|---|---|
| Search text and selected category | Storefront state | `setState()` |
| Cart quantities | `ShoppingCart` | `ChangeNotifier` and `notifyListeners()` |

The storefront owns:

```dart
final cart = ShoppingCart();
final search = TextEditingController();
String category = 'All';
```

The cart variable is final, meaning its reference is not reassigned. The cart
object itself still changes internally.

### Local updates with setState

```dart
setState(() => category = item.name);
```

This updates the category and schedules a rebuild. During `build()`, search
results are recalculated using the latest query and category.

A rebuild is not a complete application restart. Flutter processes new widget
configurations and updates the interface.

### StatelessWidget versus StatefulWidget

- `StorePage` is stateful because it owns changing screen state.
- `GroceryApp`, `ProductCard`, `QuantityControl`, `ProductSheet`, and `CartSheet`
  are stateless widgets.

Stateless does not mean the display can never update. A stateless widget may
rebuild with new inputs or contain a builder that listens to changing state.

## 9. Cart internals

`ShoppingCart` in [lib/shopping.dart](lib/shopping.dart) extends `ChangeNotifier`.

Its primary storage is:

```dart
final Map<String, int> _quantities = {};
```

Example state:

```dart
{
  'milk': 2,
  'bread': 1,
}
```

The keys are product IDs; the values are quantities. The cart does not duplicate
the full product description for each unit.

A separate lookup map connects product IDs to catalog product objects.
The cart's `products` getter uses that lookup for display and calculations.
The null assertion in that lookup relies on the invariant that only known
catalog IDs are inserted. `add()` checks this invariant.

### Adding

```dart
_quantities.update(
  product.id,
  (count) => count + 1,
  ifAbsent: () => 1,
);
notifyListeners();
```

- Existing product: increase its quantity.
- New product: insert quantity one.
- Notify interested widgets.

Unknown catalog IDs cause an `ArgumentError` rather than being silently added.

### Removing

- Above one: reduce the quantity.
- Exactly one: remove the map entry.
- Already absent: leave the cart unchanged.

Quantities cannot become negative through these operations.

## 10. Why every screen shows the same cart

The storefront creates one `ShoppingCart` and passes the same object to product
cards, product details, quantity controls, and the cart sheet.

```text
StorePage creates ShoppingCart
              |
              +--> ProductCard
              +--> ProductSheet
              +--> CartSheet
                         |
                         +--> QuantityControl
```

Passing the cart does not copy it. All these widgets refer to the same object.

Creating a separate cart in each screen would produce inconsistent quantities.

This approach is sufficient for this small app. Provider, Riverpod, or BLoC
could help with more complex application state, but none was needed here.

## 11. The reactive update flow

```text
User taps ADD
       |
       v
QuantityControl calls cart.add(product)
       |
       v
Cart updates its quantities map
       |
       v
notifyListeners()
       |
       v
Listening builders run again
       |
       v
Quantity, basket count, and prices update
```

The app uses `AnimatedBuilder` to listen to the cart:

```dart
AnimatedBuilder(
  animation: cart,
  builder: (context, _) {
    // Return widgets using current cart values.
  },
)
```

Despite the name, `AnimatedBuilder` can listen to a `ChangeNotifier`; it does
not require a visual animation.

The storefront, cart sheet, and individual quantity controls listen for updates.
This is simple but rebuilds more UI than a highly optimized large application
might. It is an acceptable tradeoff for this catalog size.

## 12. Cart calculations

All totals are derived from quantities rather than separately maintained.

```text
Item count = sum of quantities
Subtotal = sum of selling price * quantity
Savings = sum of (MRP - selling price) * quantity
Grand total = subtotal + delivery fee
```

Item count is units, not distinct products:

```text
Milk x 2 + Bread x 1 = 3 items, 2 distinct products
```

Delivery rules:

| Condition | Fee |
|---|---|
| Empty basket | INR 0 |
| Subtotal below INR 199 | INR 25 |
| Subtotal at or above INR 199 | INR 0 |

Example:

```text
Milk:  28 * 2 = 56
Bread: 45 * 1 = 45

Subtotal = 101
Delivery = 25
Grand total = 126
```

Savings are not subtracted again: the subtotal already uses discounted prices.

The `fold()` method accumulates a list of values into one result, such as adding
all line totals together.

Deriving totals prevents the quantities and displayed totals from drifting
apart after an update.

## 13. UI component hierarchy

The UI is implemented in [lib/main.dart](lib/main.dart):

```text
GroceryApp
└── StorePage
    ├── Header
    │   ├── Brand
    │   ├── Demo delivery location
    │   ├── Search input
    │   └── Cart button
    ├── Promotional banner
    ├── Category selector
    ├── Product grid
    │   └── ProductCard
    │       ├── ProductArt
    │       └── QuantityControl
    ├── No-results state
    └── Bottom cart button

Overlays
├── ProductSheet
│   ├── ProductArt
│   ├── Product details
│   └── QuantityControl
└── CartSheet
    ├── Cart entries
    ├── QuantityControl
    ├── Bill details
    └── Demo checkout dialog
```

| Component | Job |
|---|---|
| `GroceryApp` | Root application and theme. |
| `StorePage` | Own screen state and arrange the storefront. |
| `ProductArt` | Show an emoji against a product-specific background. |
| `ProductCard` | Show one catalog item and its add/quantity control. |
| `QuantityControl` | Reuse add, increase, and decrease behavior. |
| `ProductSheet` | Show information about one selected product. |
| `CartSheet` | Show basket contents, totals, and demo checkout. |

The UI file is fairly large. Keeping presentation together simplified the first
version, while the cart and catalog were separated. As it grows, these widgets
can move into dedicated files without changing the core architecture.

## 14. Navigation and bottom sheets

Product details and the cart use `showModalBottomSheet`, not separate full-page
screens:

```dart
showModalBottomSheet<void>(
  context: context,
  builder: (_) => ProductSheet(
    product: product,
    cart: cart,
  ),
);
```

The sheet receives the selected product and shared cart.

- `isScrollControlled` allows a taller sheet.
- `useSafeArea` helps avoid system intrusions.
- Width constraints keep sheets readable on desktop.
- `Navigator.pop(context)` closes the top overlay.

`BuildContext` identifies a widget's position in the tree and gives access to
nearby framework services, such as the theme and navigator.

Bottom sheets keep the storefront underneath. A larger app might use product
pages with shareable URLs and more formal routing.

The checkout button only opens an explanatory dialog. It does not send an order
request, collect a payment, or clear the basket.

## 15. Responsive layout

`LayoutBuilder` provides the space available to a widget.

### Header

Below 760 logical pixels of available width, the header stacks its brand,
location, and search into multiple rows. At larger widths, it uses one row.

Logical pixels are Flutter layout units and are not necessarily the same as
physical display pixels.

### Product grid

```dart
final columns = (constraints.maxWidth / 185)
    .floor()
    .clamp(2, 6);
```

This estimates the number of cards that fit and limits the result to two through
six columns. Cards use a fixed main-axis extent of 294 logical pixels.

### Useful layout widgets

| Widget | Purpose |
|---|---|
| `Row` | Horizontal arrangement. |
| `Column` | Vertical arrangement. |
| `Expanded` | Fill remaining space along a row or column. |
| `Flexible` | Allow a child to fit within available space. |
| `Wrap` | Move children onto another line when necessary. |
| `Padding` | Space around a child. |
| `SizedBox` | Explicit size or spacing. |
| `ConstrainedBox` | Minimum/maximum size constraints. |
| `SafeArea` | Avoid system insets. |
| `FittedBox` | Scale a child to fit available space. |
| `ListView` | A scrollable list. |
| `SingleChildScrollView` | Scroll one content block. |

The storefront uses a `CustomScrollView`. Its sections are called slivers;
`SliverToBoxAdapter` places a normal widget into that scroll system.

The product grid currently uses shrink wrapping inside the outer scroll view.
That is adequate for 18 products, but a much larger catalog should use a
lazy sliver grid and pagination.

During testing, category heights and narrow header/breadcrumb layouts caused
overflows. I adjusted constraints, flexible content, and sizing to fix those
observed issues.

## 16. Visual assets and accessibility

The design uses yellow promotional areas, green actions, rounded cards, and an
independent Daily Basket identity.

Product art uses system emoji rather than network images. This avoids external
image dependencies and copying proprietary assets, but emoji appearance varies
across devices.

The interface includes semantic labels for product art, selected category
semantics, and tooltips for several icon buttons.

These are useful basics, not a comprehensive accessibility certification.
Larger text settings, screen readers, contrast, and touch targets need broader
testing before production.

## 17. Lifecycle and cleanup

The storefront owns the cart and search controller, so it disposes them:

```dart
@override
void dispose() {
  cart.dispose();
  search.dispose();
  super.dispose();
}
```

Controllers and notifiers may hold listeners and other resources. The owner
releases them when its state is permanently removed.

Children do not dispose the shared cart because they do not own it.

The cart exists only in memory:

```text
Fresh application start -> new ShoppingCart -> empty quantities map
```

Hot reload often preserves existing state. Hot restart, a fresh app launch,
or refreshing the browser recreates the state.

## 18. Dependencies and development tools

Application dependencies in [pubspec.yaml](pubspec.yaml):

| Dependency | Purpose |
|---|---|
| `flutter` | UI framework and core Flutter libraries. |
| `cupertino_icons` | Retained generated-template dependency; the current UI uses Material icons. |
| `flutter_test` | Unit/widget testing support, under development dependencies. |
| `flutter_lints` | Recommended static-analysis rules, under development dependencies. |

No third-party networking, persistence, or state-management package was added.

Tools installed/configured for this project:

| Tool | Purpose |
|---|---|
| Flutter SDK | Run, analyze, test, and build Flutter apps. |
| Dart SDK | Dart tooling, bundled with Flutter. |
| Flutter and Dart VS Code extensions | Completion, debugging, and editor integration. |
| Android Studio | Android development environment and bundled Java runtime. |
| Android SDK | Android platform and packaging tools. |
| ADB / Platform-Tools | Communicate with Android phones and emulators. |
| Android NDK and CMake | Native Android build tooling. |
| Gradle | Coordinate Android build tasks. |

Flutter was installed at `C:\Users\annikumari\development\flutter`.
The editor SDK path is configured in
[.vscode/settings.json](.vscode/settings.json).

Environment installation is separate from application dependencies:
installing Android Studio does not add a Dart package to the app.

## 19. What was changed and why

| Change | Reason |
|---|---|
| Generated Flutter Android, iOS, and web scaffolding | Establish the platform host projects and baseline tooling. |
| Replaced the counter app in [lib/main.dart](lib/main.dart) | Implement the requested shopping UI and interactions. |
| Added [lib/catalog.dart](lib/catalog.dart) | Separate product data and search from visual presentation. |
| Added [lib/shopping.dart](lib/shopping.dart) | Centralize cart behavior, calculations, and notifications. |
| Replaced starter tests in [test/widget_test.dart](test/widget_test.dart) | Test shopping behavior rather than the generated counter example. |
| Updated [pubspec.yaml](pubspec.yaml) description | Describe the implemented app. |
| Added [.vscode/settings.json](.vscode/settings.json) | Let the editor find the installed Flutter SDK. |
| Updated [web/index.html](web/index.html) and [web/manifest.json](web/manifest.json) | Set browser/app names, descriptions, and theme colors. |
| Updated [AndroidManifest.xml](android/app/src/main/AndroidManifest.xml) | Set the Android display name to Daily Basket. |
| Updated [Info.plist](ios/Runner/Info.plist) | Set the iOS display name to Daily Basket. |
| Updated [README.md](README.md) | Document features, limitations, setup, builds, and installation. |
| Fixed layout overflows found by tests | Preserve usable narrow-phone and category layouts. |

The Dart package name remains `blinkit_clone`; the Android application ID remains
`com.example.blinkit_clone`. These technical identifiers differ from the displayed
name, Daily Basket.

## 20. Testing and verification

There are two unit tests and four widget tests in
[test/widget_test.dart](test/widget_test.dart).

### Unit tests

1. Search matches product names and brands, handles case/whitespace, and respects
   categories.
2. Cart operations calculate counts, subtotal, savings, empty-cart behavior, and
   the exact INR 199 free-delivery threshold correctly.

### Widget tests

3. Search, details, adding, cart quantity updates, demo checkout, and removal.
4. Empty search results can reset to the full catalog.
5. Category filtering, empty cart, and adding on a phone-sized viewport.
6. Narrow-phone rendering without detected overflow.

Example:

```dart
await tester.tap(find.text('Add to cart'));
await tester.pumpAndSettle();
```

`tap()` simulates interaction. `pumpAndSettle()` advances frames while pending
animations settle. `expect()` checks a result.

Keys such as `ValueKey('add-milk')` let tests identify specific controls.
The scroll helper uses gestures to reveal controls before interacting.

Validation completed during implementation:

- `flutter analyze`: no reported issues.
- `flutter test`: all six tests passed.
- `flutter build web`: successful.
- Release web server: HTTP response and bootstrap checked.
- Headless Edge: storefront screenshot rendered and inspected.
- `flutter build apk --debug`: successful after installing missing native tools.
- APK metadata inspected for application identity and Android compatibility.

The APK has not been verified on the user's physical phone. Automated checks
do not cover every device, accessibility setting, or production scenario.

## 21. Builds, signing, and Android installation

The Android build flow is approximately:

```text
Dart source + Flutter framework
              |
              v
Flutter build tooling
              |
              v
Gradle + Android SDK / native tools
              |
              v
Android APK
```

Android configuration is in
[android/app/build.gradle.kts](android/app/build.gradle.kts).
It defines the application ID, SDK settings, Java/Kotlin target settings,
version integration, and signing behavior.

The first Android build failed because its required NDK was absent and the
new command-line tools used updated package-path syntax. Installing the NDK
and CMake explicitly resolved the build blocker.

### Debug versus release

The generated APK is a development build:

```powershell
flutter build apk --debug
```

Output: `build\app\outputs\flutter-apk\app-debug.apk`.

It is approximately 144 MB and requires Android 7.0 / API 24 or newer.
Debug APKs contain development overhead and are larger than optimized releases.

The generated release configuration currently also uses a debug signing key.
A production release needs proper signing credentials, a suitable application
ID, release validation, and store preparation.

### Remote computer limitation

A phone connected to your laptop is not automatically visible to a remote VM.
ADB must run somewhere that can actually reach the phone.

Options:

- Transfer the APK to the phone and install it.
- Use supported, organization-approved USB redirection.
- Use Android 11+ wireless debugging over reachable private networking.

Wireless pairing uses a pairing port and code. The connection uses a separate
debugging port shown on the phone. A remote cloud machine usually needs approved
private VPN connectivity; Remote Desktop alone does not provide that path.
Do not expose ADB ports publicly.

## 22. Common development commands

Run from the project folder in PowerShell:

```powershell
cd C:\Users\annikumari\project\Learn
$env:Path = "C:\Users\annikumari\development\flutter\bin;$env:Path"

flutter pub get
flutter analyze
flutter test
flutter run -d edge
```

Build outputs:

```powershell
flutter build web
flutter build apk --debug
```

Find connected devices:

```powershell
flutter devices
```

Run on a detected phone by replacing the placeholder:

```powershell
flutter run -d YOUR_DEVICE_ID
```

Check environment health:

```powershell
flutter doctor
```

During a terminal-based debug run, `r` triggers hot reload and `R` triggers hot
restart. Stop the run with Ctrl+C.

## 23. Production architecture: possible next steps

These layers are future work, not existing features:

```text
Flutter UI
    |
Application / state layer
    |
Repositories
    |
    +--> Local storage
    |
    +--> API client
              |
              v
         Backend server
              |
              +--> Database
              +--> Authentication
              +--> Product catalog / inventory
              +--> Orders
              +--> Payment integration
              +--> Delivery systems
```

A repository hides the source of data so the UI need not know whether products
come from an API, cache, or local catalog.

Important future requirements:

- Persist the cart.
- Handle loading, failures, retries, and invalid external data.
- Add real product images and accessible descriptions.
- Introduce stock and quantity limits.
- Add authentication and real address handling.
- Have the server verify prices, inventory, charges, and payment status.
- Never trust the client-calculated total as the final charge.
- Paginate larger catalogs and improve rebuild granularity as needed.
- Add integration tests and real-device validation.

## 24. Suggested learning path

1. Learn Dart variables, functions, classes, lists, maps, null safety, `final`,
   and `const`.
2. Read [lib/catalog.dart](lib/catalog.dart) to understand the data.
3. Read [lib/shopping.dart](lib/shopping.dart) to understand state and totals.
4. Read application startup and theme in [lib/main.dart](lib/main.dart).
5. Study `ProductCard` and `QuantityControl`.
6. Trace `setState()` and `notifyListeners()` through a user interaction.
7. Study bottom-sheet navigation.
8. Read and run [test/widget_test.dart](test/widget_test.dart).

Beginner exercises:

- Change a price and calculate the expected discount by hand.
- Add a new product using the existing model.
- Change the free-delivery threshold and update the relevant tests.
- Add tests for multi-word search and mixed-product totals.
- Extract one UI widget into its own file without changing behavior.
- Later, add cart persistence and tests for restoring saved quantities.

## 25. The central idea

> Product data describes what is available. State records the user's choices.
> Business logic calculates the results. Widgets display those results and
> translate user actions into state changes.

Understanding that loop is more important than memorizing every widget.
