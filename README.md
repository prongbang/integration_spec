# integration_spec

[![pub package](https://img.shields.io/pub/v/integration_spec.svg)](https://pub.dartlang.org/packages/integration_spec)

Generate Page Object Model dart class by spec.yml

## Features

- Generate Page Object Model dart class by spec.yml

## Getting started

```yaml
dev_dependencies:
  integration_test:
    sdk: flutter
  flutter_test:
    sdk: flutter
  build_runner: ^2.1.11
  integration_spec: ^0.0.1
```

or

```yaml
dev_dependencies:
  integration_spec:
    git: https://github.com/prongbang/integration_spec.git
```

## Usage

- Create spec `login_test_spec.yml`

```yaml
screen: "Login"
specs:
  verify_hello_text:
    text: "Hello"
    type: "verify"
    matcher: "one" # "nothing", "one", "2"
  tap_next_button:
    key: "nextButton"
    type: "tap"
    delay: 500
  enter_number_field:
    key: "numberField"
    type: "enter"
    text: "123456"
```

## Run to generate in root project

```shell
dart run build_runner build 
```

## Output

- login_test_spec.screen.dart

```dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';
import 'package:integration_spec/integration_spec.dart';
import 'package:integration_spec/widget_tester.dart';

class LoginTestScreen extends TestScreen {
	final WidgetTester tester;

	LoginTestScreen(this.tester) {
		tester.printToConsole('✓ Login Screen Open');
	}

	Future<void> verifyHelloText() async {
		final finderVerifyHelloText = find.text('Hello');
		await tester.pumpAndSettle();
		await tester.pumpUntilFound(finderVerifyHelloText);
		expect(finderVerifyHelloText, findsOneWidget);
		await tester.pumpAndSettle();
	}

	Future<void> tapNextButton() async {
		final finderTapNextButton = find.byKey(const Key('nextButton'));
		await tester.delay(500);
		await tester.pumpAndSettle();
		await tester.pumpUntilFound(finderTapNextButton);
		await tester.tap(finderTapNextButton);
		await tester.pumpAndSettle();
	}

	Future<void> enterNumberField() async {
		final finderEnterNumberField = find.byKey(const Key('numberField'));
		await tester.pumpAndSettle();
		await tester.pumpUntilFound(finderEnterNumberField);
		await tester.enterText(finderEnterNumberField, '123456');
		await tester.pumpAndSettle();
	}

	@override
	Future<void> run() async {
		await verifyHelloText();
		await tapNextButton();
		await enterNumberField();
	}
}
```

- login_test.dart

```dart
import 'package:flutter_test/flutter_test.dart';
import 'package:integration_test/integration_test.dart';

import 'login_test_spec.screen.dart';

void main() {
  IntegrationTestWidgetsFlutterBinding.ensureInitialized();

  testWidgets(
    'Should OTP success when Login and OTP success',
    (tester) async {
      // Login Screen
      await LoginTestScreen(tester).run();
    },
  );
}
```