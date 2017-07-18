# Common dev mistakes with Angular Universal

## Universal, what is it?
TL;DR: Server side rendering of your Angular project!

Universal takes your Angular 2 project and renders it on the server. By running your component and template compilation on the server, user experience faster load times. Using AoT(Ahead of time) compilation also allows you to run api calls for data fetching. This is great for SEO!

## The setup
Most of Angular's problems are due to the setup. That is an entirely different article all together and you for most projects you can use a [starter repo](https://github.com/AngularClass/angular-starter) or [cli](https://github.com/angular/angular-cli) to avoid any setup issues.

## Window Issues
Due to the application being rendered on the server, you lose access to all things browser. This includes access to the Window object and the DOM. This generally isn't an issue as long as you aren't directly querying the DOM using methods like:
```javascript
  document.getElementById()
  document.querySelector()
```
Instead, use Angular directives like ```viewChild/viewChildren```:
```javascript
import {Component, ViewChild} from '@angular/core'
@Component({
  template: `
  <div>
    <p #myChild>Pick me!</p>
  </div>
  `
})
export class PickMe{
  @ViewChild('myChild') pickMe;
}
```
_You should never wrap code in a setTimeout just to avoid build errors as it will slow your rendering_

Always make sure any external libraries are Universal ready as they can break [AoT](https://angular.io/guide/aot-compiler) and cause your app to not build/work properly.

## When browser, when server
Possibly the biggest gotcha is knowing when your component is being rendered on the server or the browser and only executing  code when needed. Luckily for us(and you), Universal comes with (tools)[https://github.com/angular/universal#universal-gotchas] to solve this issue. Keep in mind server calls your component makes will be called on the server and in the browser. It is highly recommended to either wait for the component to render on the browser to make the call or simply cache the api output data on the server. As expected, making multiple calls to an api makes your app sluggish on load.

## Jquery & Angular
__DONT USE IT__ It's that simple. If you need to query the DOM, set a ```viewChild/viewChildren```. Need to change styles, use ```ngClass/ngStyle```. Need a reference to an element, use ```ElementRef```. Jquery always introduces more problems than it fixes. This does not include libraries such as D3.