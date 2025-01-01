---
layout: post
title: How to Use Pseudo-private CSS Variables
metadescription: A Guide to Simplifying Customisable CSS Components
comments: true
---
{::options auto_ids="false" /}
## Introduction to CSS Custom Properties
{: .dotted .white-dots}
When building reusable components, it's often useful to expose style properties via CSS custom properties, or CSS variables. This allows developers to easily customize the look and feel of components. One popular approach is using variables like `--color` to manage multiple style aspects. However, there are more efficient ways to handle fallbacks for these properties.
## Basic Example with CSS Variables
{: .dotted .white-dots}
Suppose you're styling a button, and you want to let developers customize its color using a `--color` variable. You can set it like this:

```css
.fancy-button {
  border: .1em solid var(--color);
  background: transparent;
  color: var(--color);
}

.fancy-button:hover {
  background: var(--color);
  color: white;
}
```
However, if `--color` is not defined, you'll get an unstyled button with no color.
## Adding Default Values
{: .dotted .white-dots}
To avoid unstyled components, you could add a fallback value directly in the `var()` function:

```css
.fancy-button {
  border: .1em solid var(--color, black);
  background: transparent;
  color: var(--color, black);
}

.fancy-button:hover {
  background: var(--color, black);
  color: white;
}
```
While this works, it results in repetitive and verbose code.
## Common Optimization Using Internal Variables
{: .dotted .white-dots}
A more common approach is to declare the fallback value directly as the `--color` variable in the component:

```css
.fancy-button {
  --color: black;
  border: .1em solid var(--color);
  background: transparent;
  color: var(--color);
}

.fancy-button:hover {
  background: var(--color);
  color: white;
}
```
This simplifies things, but introduces issues with inheritance and specificity. It also forces developers to know and override your internal rules if they want to customize the component.
## Introducing Pseudo-private Custom Properties
{: .dotted .white-dots}
A better solution is to use a pseudo-private custom property. This involves using an internal variable (e.g., `--_color`) and assigning it the public custom property (`--color`) with a fallback:

```css
.fancy-button {
  --_color: var(--color, black);
  border: .1em solid var(--_color);
  background: transparent;
  color: var(--_color);
}

.fancy-button:hover {
  background: var(--_color);
  color: white;
}
```
This method ensures that your components have a default style while still allowing for easy customization. It also reduces the likelihood of conflicts with other styles and avoids specificity issues.
## Why Use Pseudo-private Variables?
{: .dotted .white-dots}
By using pseudo-private variables like `--_color`, you can keep internal logic separate from what’s exposed to developers. This way, you maintain flexibility in how your components are styled while preventing potential conflicts. Even though these variables aren't truly private, this pattern has been used in JavaScript for decades with success.
## Conclusion
{: .dotted .white-dots}
Pseudo-private CSS variables are a powerful way to build customizable, maintainable, and conflict-free components. By separating internal logic from what you expose, you provide flexibility to developers while ensuring your components behave consistently. This approach keeps your codebase clean and avoids repetitive fallbacks.
