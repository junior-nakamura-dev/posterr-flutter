# Overview

The idea of this project is to evolve the architecture and understand more about micro frontend using flutter, for a business case of a posting application similar to Twitter that will integrate with a backend developed by me following the backend link (https://github.com/junior-nakamura-dev/sboot-posterr).

# Credits

The base micro-app project structure was forked from the profile of my fellow Deivid Willyan one of the biggest influencers in the Flutter community in Brazil this is the following link for his profile https://github.com/DeividWillyan/). I forked from Deivid profile because I understood this is a solid basis to start.

# Step by Step to running the app

1 - Pull all the microapps in your machine. Use the following command below to do it.

- git submodule update --init --recursive

# Archictecture definition & Design Patterns to the project

![alt text](architecture.png)

---

## Versioning

Don't forget to always update the version tag in pubspec.yaml using the following pattern below.

```yaml
version: 1.0.1
```

You must need to log each update did in the project in CHANGELOG.md file using the following pattern below

```md
## [1.0.1] - Fix Dependencies - 21/04/2021 - [fix/fix-dependencies]

- Updated dependencies in pubspec.yml file
```

The versioning is using tags.

```bash
List Tags: git tag -n
Create Tag: git tag 'v<mejor.minor.patch>'
Push Tags: git push --tags
```

To import created versions has to following the pattern below:

```yaml
dependencies:
  micro_core:
    git:
      url: https://github.com/junior-nakamura-dev/posterr-flutter-micro-core.git
      ref: v1.0.0 # <- Put the versioned tag here
```

| PS: You can use dependecy_overrides to solve version conflict

---

## Tests

We will use 4 approches for testing. Unit test, Widget test, Golden Test and Integration/Driver.

### Unit Test

We will use the Unit test to validate the business rule and development logic (Pure dart code)

```dart
test('Should LoginController return true in login method with correct values', () async {
    bool result = await _controller.login(user, pass);
    expect(result, true);
});
```

### Widget Test

We will use the Widget test to validate the Pages if it is rendering as expected

```dart
testWidgets("Should find a single widget per view on screen rendering", (WidgetTester tester) async {
    await tester.pumpWidget(MaterialApp(home: LoginPage()));

    final appBarTitle = find.text('Login');
    expect(appBarTitle, findsOneWidget);
});
```

### Golden Test

We will use Golden test to validate the components in Design System module. We will look if the components was created as expected with a mockup feedback fast and effective.

```dart
// flutter_test_config.dart
import 'dart:async';

import 'package:golden_toolkit/golden_toolkit.dart';

Future<void> testExecutable(FutureOr<void> Function() testMain) async {
  return GoldenToolkit.runWithConfiguration(
    () async {
      await loadAppFonts();
      await testMain();
    },
    config: GoldenToolkitConfiguration(),
  );
}

// custom_button_widget_test.dart
testGoldens("CustomButtonWidget - Multiples Scenarios", (tester) async {
    final builder = GoldenBuilder.grid(columns: 2, widthToHeightRatio: 1)
      ..addScenario(
        'Default',
        CustomButtonWidget(
          text: 'CustomButton',
          onPressed: () {},
        ),
      )
      ..addScenario(
        'Outlier',
        CustomButtonWidget(
          text: 'CustomButton',
          onPressed: () {},
          outline: true,
        ),
      )

    await tester.pumpWidgetBuilder(
      builder.build(),
      surfaceSize: const Size(500, 300),
    );
    await screenMatchesGolden(tester, 'custonButton_multiples_states');
  });

// RUN
flutter test --update-goldens
```

### Integration/Driver Test

We will use the Integration/Driver test to ensure the application is working in a end-to-end approach.

```dart
// test_driver/integration_test.dart
import 'package:integration_test/integration_test_driver.dart';

Future<void> main() => integrationDriver();

// integration_test/complete_app_test.dart
void main() {
  IntegrationTestWidgetsFlutterBinding.ensureInitialized();
  testWidgets('Complete App Navigation Flux', (WidgetTester tester) async {
    await tester.pumpWidget(MyApp());

    final btnGoToHomePage = find.text('goToHome');
    expect(btnGoToHomePage, findsOneWidget);

    await tester.tap(btnGoToHomePage);
    await tester.pumpAndSettle();

    final pageHomeTitle = find.text('Home');
    expect(pageHomeTitle, findsOneWidget);

    await tester.pageBack();
    await tester.pumpAndSettle();

    final pageLoginTitle = find.text('Login');
    expect(pageLoginTitle, findsOneWidget);
  });
}

// Run
flutter drive --driver=test_driver/integration_test.dart --target=integration_test/complete_app_test.dart

```

Usefull Links

- [How to Create Git Tag](https://devconnected.com/how-to-create-git-tags)
- [Semantic Versioning 2.0.0](https://semver.org/)
- [Lab: Write your first Flutter app](https://flutter.dev/docs/get-started/codelab)
- [Cookbook: Useful Flutter samples](https://flutter.dev/docs/cookbook)
- [Flutter Test](https://flutter.dev/docs/testing)
- [Integration Test](https://flutter.dev/docs/cookbook/testing/integration/introduction)
- [Golden Test](https://pub.dev/packages/golden_toolkit)

---
