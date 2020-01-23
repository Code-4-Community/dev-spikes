# Vue Access Control Layer
Described below are possible specifications for an access control layer in Vue. i.e., a method of conditionally rendering components or elements of components in a Vue application. This is *not* the same as conditionally rendering an entire page.


### Method 1: v-if
Use v-if in a Component tag to render if and only if the user's current rank (integer) passes some predicate. A delete button for an event, for example:

```html
<Delete v-if="canDelete(current_user, event_user_id)" />
```

```javascript
function canDelete(current_user, event_user_id) {
  return current_user.id === event_user_id
      || isAdmin(current_user)
}
```

#### Pros
  - No external library to learn and keep track of

#### Cons
  - Scales... acceptably? Not terribly, despite initial impressions. Some circumstances:
    -  Want to change what information is needed from the user to pass the predicate
    -- That logic will be in one place anyway, where the predicate is implemented. Will only have to be changed there
    - Want to add a new role that can see that component. E.g., you have Users, Moderators, and Admins. Admins can already see it, want to add Moderators
    -- Again, can just add an 'or' clause or something similar to the predicate to also check for moderators
    - Say components A and B can both be deleted. Say one is an entire event and another is a comment on that event page. You only want Admins to be able to delete entire events, but you want both admins and moderators to be able to delete comments. You then have to implement two similarly named but different *delete* predicates. This may end up not being a concern for the scale of our projects, but just for example:
    ```javascript
    function canDeleteEvent(user_role) {
        return user_role === 2 // where 2 corresponds to 'admin' in a role table
    }

    function canDeleteComment(user_role) {
        return user_role >= 1 // where 1 corresponds to 'moderator' in the same table, and 2 is again 'admin'
    }
    ```





### Method 2: AccessControl component

Use a wrapper component that renders interior content only if a given user's role is included in a pre-determined list of acceptable roles. Full credit goes to the author of [this article](https://dev.to/umarov/easy-access-control-in-your-vue-apps-hhp).

**AccessControl.vue**. [More information about the 'slot' tag](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/slot)
```html
<template>
  <div v-if="hasRole">
    <slot />
  </div>
</template>

<script>
export default {
  props: {
    roles: { type: Array, default: () => [] },
    role: { type: String, default: '' }
  },
  computed: {
    hasRole () {
      return this.roles.length === 0 || this.roles.includes(this.role)
    }
  }
}
</script>
```

**EventsList.vue**
```html
Button would be rendered.
<access-control :roles="['admin']" role="admin">
  <button v-on:click="alert('I'm an admin!')">If you're an admin you can see me<button/>
</access-control>

Button would not be rendered
<access-control :roles="['admin']" role="user">
  <button v-on:click="alert('I'm an admin!')">If you're an admin you can see me<button/>
</access-control>
```

#### Pros
  - Readability. Extremely easy to see what elements of a component or what components on a page are concerned with roles. A *v-if* could be anything, until you do a double take to find out. It's easier to see what's wrapped in AccessControl and then check for what roles it accepts on-the-fly.

#### Cons (or just a neutral?)
  - As far as I can tell, almost exact same scalability as just a plain v-if. So with the same scalability but better readability I would pick this method.

### Method 3: CASL

Use the CASL library to manage the access control layer
Summary to follow. [Overview](https://scotch.io/tutorials/managing-user-permissions-in-vue-using-casl)
