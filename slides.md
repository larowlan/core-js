# Core javascript issues 🦄

---

## Scenario 1️⃣
### Contrib dev wants to ship a React component

Note:
- This could be Vue, Svelte w/e
- At present they have their own package.json, their own build process
- This may bundle other open source NPM packages/components into a dist file
- They commit the dist file to the repo and add it to their libraries.yml

---

## Issues 🙃

Note:
- No visibility into dependencies for consuming sites
- No ability to leverage npm audit for consuming sites
- If site also wants to install wizzbang Toolbar that is also React powered, two versions of React on the one page

---

## Scenario 2️⃣
### Site owner wants to include an npm package

Note:
- primitive example, they have an image library and want to use an existing solution
- but this could also be a framework specific package like e.g. a React based dialog or similar

---

## Issues 😵‍💫

Note:
- How do they include it? 
- Do they add a package.json in the theme?
- Do they add it in the project root?
- How do they deal with packaging concerns (e.g. no node_modules in webroot)

---

## Scenario 3️⃣
### CKEditor contrib dev

Note:
- Consider scenario where maintainer maintains two CKEditor contrib modules
- Both modules have their own package.json

---

## Issues 😕

Note:
- Largely the same as the contrib scenerio 1 but exacerbated
- Both have their own node_modules
- If they have common dependencies, these could be bundled twice

---

## Scenario 4️⃣
### Contrib wants you to install an npm library

Note:
- So in this scenario they're not bundling
- They want you to install it yourself

---

## Issues 😡

Note:
- Currently manual download or asset-packagist
- No visibility of dependencies, security tools like npm audit
- 1-N complication (once per contrib module)
- Archive tar analogy
- Asset-packagist
-- We tell people to use asset-packagist and move a FE concern to a BE system
-- It relies on a Ukrainian web agency who provide a packagist repo (json file) mapping FE third part dependencies to composer
-- Modules like webform shipping this prevents people from updating to resolve security issues

---

## So what's the solution ✨

Note:
- So the archive tar analogy shows us the way here
- We had a composer intiative for BE - but nothing for FE
- We need to bring our FE packaging tooling in line with what other projects provide

---

## What do other projects do 📋️

Note:
- Symfony has 'encore' which is starting webpack config
- Laravel has Mix, also webpack
- Wordpress has baked in webpack
- Some of our contrib themes ship Laravel mix config
- I prefer Vite and rollup, but given CKEditor uses webpack, it makes sense

---

## The libraries.yml conundrum

Note:
- Adding a webpack build is a start, but we also could do more to reduce the complexity here
- We just had an issue with library weights where the solution was to divide the weights by a higher number
- We can use webpack to generate a manifest and try to automate some of the library generation
- Can we just let devs use the new attach js/css?

---

## 💧Drupalisms

Note:
- Drupal.behaviors is not needed if we move to type=module
- We can use import in the browser with our browser list
- We have 'attach_js' function, this could be enough for entry point

---

## ☺️ This looks familiar

```javascript
import { drupalSettings } from '@drupal/core';
import { somethingCommenty } from '@drupal/core/comment';

const myThing = () => {
  somethingCommenty(document.querySelector('.my-thing'), drupalSettings.myThing);
}

export default myThing;

```

Note:
- We can use aliases so that `import something from '@drupal/core/comment'` or `import something from '@drupal/contrib/something` work


---

## 🤔 This does not

```javascript

(({behaviors, myThing}) => {
  behaviors.myThing = {
    attach: (context) => {
      Drupal.somethingCommenty(document.querySelector('my-thing'), myThing)
    }
  }
})(Drupal, drupalSettings)
```

---

## 🤷 Why bother?

Note:
- Making our JS consistent with the ecosystem will bring in new contributors 
- Improve our ability to innovate