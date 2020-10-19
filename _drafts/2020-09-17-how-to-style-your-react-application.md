---
layout: post
title: How to style your React application?
slug: how-to-style-your-react-application
date_published: 2020-09-17T20:46:46.000Z
date_updated: 2020-09-17T20:50:29.000Z
tags: React
excerpt: We as developers focus on how things work, not on how they look. Until we realize that our little baby is ugly 😭 so then we start styling and resizing here and there to make it prettier.
But how the heck can we style all of them in a way that is efficient and makes sense?
---

> I built my first app. It works. Now how the heck do I go about styling it?

When building a React application, you get to a point where it is fully functional but it doesn't look like you want.

We as developers focus on how things work, not on how they look. Until we realize that our little baby is ugly 😭 so then we start styling and resizing here and there to make it prettier.

But how the heck can we style all of them in a way that is efficient and makes sense?

## The fastest way to style your React app

As you may be guessing, the fastest way to style your React app is by using a *UI library* that do all the heavy lifting for you.

When using UI libraries, instead of playing with margins, paddings and sizes, you just use the components the library provides. In the end, you save time and win styling consistence.

The next question is: *What UI library should you use?*

There is not an "industry standard" when choosing library, but the [commonly](https://maxrozen.com/guide-to-component-ui-libraries-react/) used are:

- [Ant Design](https://ant.design/)
- [Bootstrap](https://reactstrap.github.io/)
- [Bulma](https://github.com/couds/react-bulma-components)
- [Chakra UI](https://chakra-ui.com/)
- [Material UI](https://material-ui.com/)
- [Semantic UI](https://semantic-ui.com/)

My personal choice is Bootstrap or Ant Design. Bootstrap for simple apps without many functionalities and Ant Design for the rest.

Why Bootstrap? I have experience building static sites with HTML + CSS + Bootstrap. So, I can leverage my knowledge in this framework with React. 

Why AntD? Two reasons. The first one: **the documentation is awesome**. I rarely google AntD stuff when using this framework. The second reason is that **it has MANY components**. When using it, I feel like they have though in everything. Its magic.

MaterialUI documentation is pretty good too. But in my experience it is a bit harder to customize and I feel like all apps made with material design looks like Google apps. Which is not bad BTW, I just don't like it.

I can't say much about the rest because I haven't used them, but I know that they are safe bets. So, my advice is: **choose the one that looks similar to what you want to accomplish**.

At some point in the project, you will realize that your app needs some personalized touch. Which leaves us to...

## Color personalization: how to choose your App colors?

Many libraries have primary or secondary colors you can change. The defaults are good, but what if you want to add *personality* to your app? What colors should you use?

My design knowledge is zero. So, I usually use palette color tools such as [colorhunt](https://colorhunt.co/) or [coolors](https://coolors.co/), where you can pick one visually:
![](/content/images/2020/09/Screen-Shot-2020-09-17-at-14.42.50.png)Color Hunt example
For a [Chilean Tax payment calculator](https://impuestos.netlify.app/) I made, the colors I used was a variation of [this palette](https://colorhunt.co/palette/22672):
![](/content/images/2020/09/impuestos-demo.gif)Color hunt in action
Using palettes, you can choose one that looks good for your project.

If you already know the main color, you can use [Adobe Color Wheel](https://color.adobe.com/create/color-wheel) to get the rest:
![](/content/images/2020/09/Screen-Shot-2020-09-17-at-15.41.47.png)Adobe Color Wheel
Personalization is not all about colors. Sometimes you will have to make a component and styles by yourself.

> [7 Practical Tips for Cheating at Design](https://medium.com/refactoring-ui/7-practical-tips-for-cheating-at-design-40c736799886) is a good post to avoid common design mistakes and to make your apps looks more professional.

There are many ways to add styles to your React components. See [How to CSS Style in React](https://www.robinwieruch.de/react-css-styling) for a good summary of the approaches. I have been used mostly [css-in-js](https://cssinjs.org/?v=v10.4.0) with [react-jss](https://cssinjs.org/react-jss/?v=v10.4.0), but I don't have a strong opinion about this.

If you're lacking of design ideas, knowing how to add your own style is not enough. And when you're lacking of ideas...

## Where to look for inspiration?

When I'm lost, I usually go to [Dribbble](https://dribbble.com/shots/following/web-design) for inspiration. Or I look at the apps I already use.

For example, I was building an app with Chat functionality and the framework ([React with Ionic](https://ionicframework.com/docs/react)) didn't have components for this use case. I ended with this:
![](/content/images/2020/09/Screen-Shot-2020-09-17-at-15.53.16.png)Chat example
Which was based in this [dribbble](https://dribbble.com/shots/6261272-Mobile-Leaderboard) and on [Facebook Messenger](https://www.messenger.com/).

## Final notes

In summary:

- The fastest way to make a good-looking React app is by using a UI library. Choosing a [popular](https://maxrozen.com/guide-to-component-ui-libraries-react/) one is a safe bet, so choose one based on what you are going to build.
- The low-hanging fruits in personalization are colors. To choose a good set of colors, you can use [Color Hunt](https://colorhunt.co/).
- When the UI framework is not enough, you'll need to make a custom component from scratch. You can use [tactics](https://medium.com/refactoring-ui/7-practical-tips-for-cheating-at-design-40c736799886) to improve your design and you have [many choices](https://www.robinwieruch.de/react-css-styling) to style it. Lacking of ideas? Use [Dribbble](https://dribbble.com/shots/following/web-design) for inspiration.

**Bonus:** just found [Design Resources for Developer](https://github.com/bradtraversy/design-resources-for-developers) in GitHub, looks awesome!

---

Hey, I wrote a 10-page e-book about you can download for FREE 🎁 to overcome analysis paralysis when building React applications. It is about:

1. Code styling and quality.
2. Organizing your project files.
3. How to manage your state.

You can grab it below:
