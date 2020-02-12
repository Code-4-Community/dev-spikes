# Vue Router Page Guard
In other words, protecting a page by only allowing users with sufficient permissions to use the route to it

[Full Vue Documentation](https://router.vuejs.org/guide/advanced/navigation-guards.html#in-component-guards). It's not a very complex topic, but here is a short summary:

### Per-Route Guard
Adding a guard on a specific route. Say, protecting a route that goes to some sort of admin panel page where an adminstrator can review and change data (e.g. users).

```javascript
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      }
    }
  ]
})
```

Where **to** is a Route to be routed to, **from** is the Route being navigated away from, and **next** is a function that resolves the hook depending on what arguments it is given.

No arguments in **next**, simply **next()**, resolves the hook and allows the navigation to happen.

**next(false)** will block the route and re-route back to the **from** route.

**next('/') or next({ path: '/' })** will redirect to a different location than the **to** location.

### Working in Tandem with dynamically shown components

This functionality will supplement the ability to dynamically render individual components. If an entire page is only relevant to administrative users, it makes more sense to restrict that page using this method than to allow users to route to the page and see no content or a simple message.

