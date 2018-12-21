## Mustache Templates in Citron

This implementation of [Mustache](https://mustache.github.io) is _heavily_ based off (read: a 90% copy) of the [Smalltalk implementation](https://github.com/noha/mustache).

### Usage
Example taken off the documentation:

```
var templateString is ‘Hello {{ name }}
You have just won ${{value}}!
{{#in_ca}}
Well, ${{taxed_value}}, after taxes.
{{/in_ca}}’.
```

Given a context:
```
var context is Map new
  put: 'Chris' at: 'name',
  put: 10000 at: 'value',
  put: 10000 * 60 / 100 at: 'taxed_value',
  put: True at: 'in_ca'.
```

we can compile, and then render the template:
```
var rendered is MustacheTemplate new init compile: templateString, value: context.
```

alternatively:
```
var rendered is templateString asMustacheTemplate value: context.
```

which produces the following:
```
Hello Chris
You have just won $10000!
Well, $6000.0, after taxes.
```

Note: There will be some newlines and extra whitespace, which the spec does not talk about.

### Lists
Arrays can be used to generate looped constructs in the template;

given this template:
```
A list of numbers
{{# list }}
Number: {{.}}
{{/ list }}
```

and a context map like so:
```
Map new
  put: [1,2,3,4,5,6] at: 'list'
```

will produce the result:
```
A list of numbers
Number: 1
Number: 2
Number: 3
Number: 4
Number: 5
Number: 6
```

Blocks are also allowed. They're evaluated at render time:
```
A list of {{what}}
{{#list}}
Number: {{.}}
{{/list}}
```
with this context:
```
Map new
  put: [1,2,3,4,5,6] at: 'list',
  put: {^'numbers'.} at: 'what'.
```
will generate this:
```
A list of numbers
Number: 1
Number: 2
Number: 3
Number: 4
Number: 5
Number: 6
```

### Partial Templates
Templates have a notion of sub templates that are called partials.

```
var templateString is '
<h2>Names</h2>
{{# names }}
{{> user }}
{{/ names }}'.
var userTemplateString is '<strong>{{.}}</strong>'.
templateString asMustacheTemplate
    value: (Map new
      put: ['AnotherTest', 'CxByte'] at: 'names')
    partials: (Map new
      put: userTemplateString at: 'user').
```

will generate:
```
<h2>Names</h2>
<strong>AnotherTest</strong>
<strong>CxByte</strong>
```
