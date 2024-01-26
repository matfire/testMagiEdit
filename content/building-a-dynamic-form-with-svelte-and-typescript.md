---
title: "Building a dynamic form with Svelte and Typescript"
series: "magiedit"
tags:
- typescript
- svelte
- javascript
---

In the quest to make [magiedit](https://magiedit.magitools.app) always better, I figured out that people may want to publish to multiple "accounts" on the same platform. So, the goal for the next feature was to allow just that. A user should be able to select the platform they want to create a **Publisher** for, fill in the required data, and be able to publish to it.
The problem was in the second to last step; the form. How do you handle the fact that different platforms require different authenticating elements to send requests?

## Generating a form from a class

As mentioned in a previous article, all the platforms have a decorator that adds them to a list that can be imported in other files, meaning I could get a list of supported platforms and generate a `select`, allowing the user to select the platform they wanted to configure. The next step consisted of having a way for the class to describe the input it needs to handle publishing an article.

### Describing form elements

After some deliberation, I settled on the following structure:

```typescript
interface IPlatformSetting {
	type: string;
	name: string;
	label: { htmlFor: string; value: string };
	settings: Record<string, unknown>;
}
```
- type defines what form element will be rendered (i.e. input)
- name: the name of the element (pretty self explanatory, I know)
- label: the settings for the label (both the `for` attribute and the actual text inside it)
- settings: an object of settings for the html element; as an example, here's what the Dev.to platform settings look like:
```typescript
{
	type: 'input',
	name: 'api_token',
	label: { htmlFor: 'api_key', value: 'API Key' },
	settings: { type: 'text', required: true }
}
```
There still is a bit of information duplication, but for now it works pretty well.

### Displaying the form elements

Once having the user select the platforms they want to publish on, the next step is to display all the fields necessary for that specific platform. Remeber how we had a method that described precisely this? This is where we get to use it.

