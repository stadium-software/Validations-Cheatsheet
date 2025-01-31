# Validation Expressions Cheat Sheet <!-- omit in toc -->

This readme describes how to create validations in Stadium 6.12+. 

## Contents <!-- omit in toc -->
- [Overview](#overview)
  - [Stadium Version](#stadium-version)
- [Required / Not Required](#required--not-required)
- [IsValid Rule](#isvalid-rule)
  - [Copy-and-Paste Expressions](#copy-and-paste-expressions)
    - [IsEmail](#isemail)
    - [IsAmount](#isamount)
    - [IsNumber](#isnumber)
    - [IsURL](#isurl)
    - [Text length is 8 or more](#text-length-is-8-or-more)
    - [Password validation](#password-validation)
    - [Characters only](#characters-only)
  - [Use AI to generate a RegEx](#use-ai-to-generate-a-regex)
  - [Date Validations](#date-validations)
    - [Date Range (DatePicker)](#date-range-datepicker)
  - [Number Validations](#number-validations)
    - [Number Range](#number-range)
  - [Combining criteria](#combining-criteria)
- [Setting IsValid and Error Texts in scripts](#setting-isvalid-and-error-texts-in-scripts)

# Overview

The release of Stadium 6.12 brings some changes to how Control validations work in Stadium. Instead of a simple selection from a limited set of predefined options, we can now use expressions to flexibly validate control values and properties as well as the values and properties of related controls. 

Full feature set:

1. Custom validation definition using Javascript expressions
2. Custom error message definition
3. Programatic triggering of Control validations
4. Programatic error message definition

When upgrading a pre- 6.12 application in the 6.12 Stadium Designer, older validations will automatically be upgraded. 

## Stadium Version
6.12+

# Required / Not Required
To mark a Control as required, check the "Required" checkbox and enter a validation message

![](images/PropertiesPanel-Required.png)

# IsValid Rule
The "IsValid Rule" property accepts a Javascript expression. If the Control value passes the expression the IsValid property is true and the Control has passed the validation. 

If the Control value does not pass the expression the IsValid property is false, the Control is placed in an error state and the error message is displayed under the Control. 

## Copy-and-Paste Expressions
A wide range of validations can be performed with the help of regular expressions. However, regular expressions are not always easy to write. Here are regular expressions for all current Stadium validations. 

To use these examples, adjust the Controlname ('TextBox' in this example) and copy & paste any of these expressions into the "IsValid" rule in the properties panel

### IsEmail
Required
```javascript
/^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|.(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(TextBox.Text)
```

### IsAmount
Required
```javascript
/^\d+(\.\d{1,2})?$/.test(TextBox.Text)
```

### IsNumber
Required
```javascript
/^\d+$/.test(TextBox.Text)
```

### IsURL
Required & with http / https
```javascript
/https?:\/\/[-a-z0-9@:%._\+~#=]{1,256}\.[a-z0-9()]{1,6}\b([-a-z0-9()@:%_\+.~#?&//=]*)/i.test(TextBox.Text)
```

### Text length is 8 or more
Required
```javascript
TextBox.Text.length > 7
```

### Password validation
Rules: 8 – 16 characters, at least one number, at least one special character
```javascript
/^(?=.*[\d])(?=.*[!@#$%^&*])[\w!@#$%^&*]{8,16}$/.test(TextBox.Text)
```

### Characters only
Required
```javascript
/^[a-zA-Z]*$/.test(TextBox.Text)
```

## Use AI to generate a RegEx
If you need a specific RegEx, but are not sure how to write it, I came across a function in the Google Gemini AI tool that will generate a RegEx from a text prompt. 

[Google AI Studio RegEx Text Prompt](https://aistudio.google.com/app/prompts/regexed)

Here is an example prompt for a complex RegEx:

```text
Give me a JavaScript regex that checks a string for the following:
The string must have 8-24 characters.
The string must contain upper and lowercase characters.
The string must contain at least one number.
The string must have at least one of the following special characters: ~!@#$%^&*()_+=-:;<,>.?
```

The result:
```javascript
/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[~!@#$%^&*()_+=-:;<,>.?]).{8,24}$/
```

Implementing this as a Stadium Validation:

Required
```javascript
/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[~!@#$%^&*()_+=-:;<,>.?]).{8,24}$/.test(TextBox.Text)
```

## Date Validations

### Date Range (DatePicker)
Required & date between Jan 1, 2023 & today
```javascript
DatePicker.Date >= new Date('01/01/2023') && DatePicker.Date <= new Date()
```

## Number Validations

### Number Range
Required & number between 1 and 12
```javascript
TextBox.Text > 0 && TextBox.Text < 13
```

## Combining criteria
To require values from one or from multiple Controls to adhere to multiple criteria (x AND y), the criteria can be combined by adding a double ampersand (&&)

Example
```javascript
CheckBox.Checked && DatePicker.Date >= new Date('01/01/2023')
```

To require values from one or from multiple Controls to adhere to any listed criteria (x OR y), the criteria can be combined by adding a double pipe (||)

Example
```javascript
new Date(DatePicker.Date).getFullYear() == 2024 || new Date(DatePicker.Date).getFullYear() == 2025
```

# Setting IsValid and Error Texts in scripts

Scripts that process Control data are triggered only after all Controls have passed the validation defined in the "IsValid Rule" property. Where necesssary additional validations can then be run and the "IsValid" and Error Text propertiesy can be set for on any Control using *SetValue* action. 