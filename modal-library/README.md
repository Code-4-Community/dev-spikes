
# Vue-js-Modal [Spec]
##### By Gabriel Peter

### Library Repo: https://github.com/euvl/vue-js-modal
### Demo: http://vue-js-modal.yev.io/

### Three Types of Modals
- "Modal"
- "Dialogs"
- "Dynamic Modals"

## Simple Modal:
 In the component template:

    <modal name="hello-world">
    hello, world!
    </modal>

*Notice how we can optionally give this renderless component a name. This is so that we can call that specific modal from anywhere within the project.*

Call it via:

    methods: {
        show () {
            this.$modal.show('hello-world');
        },
        hide () {
            this.$modal.hide('hello-world');
        }
    }   

Features: 
* Modals can receive JSON data
* '@before-open' is a built in conditional for functions
  > <modal name="hello-world" @before-open="beforeOpen"/>

## Dialogs
In component template:

    <v-dialog/>
Call it via:

    this.$modal.show('dialog', {
        title: 'Alert!',
        text: 'You are too awesome',
        buttons: [
            {
            title: 'Deal with it',
            handler: () => { alert('Woot!') }
            },
            {
            title: '',       // Button title
            default: true,    // Will be triggered by default if 'Enter' pressed.
            handler: () => {} // Button click handler
            },
            {
            title: 'Close'
            }
        ]
    })

## Dynamic Modals
In component template:

    <modals-container/>
Call it via:

    this.$modal.show({
        template: `
            <div>
            <h1>This is created inline</h1>
            <p>{{ text }}</p>
            </div>
        `,
        props: ['text']
        }, {
        text: 'This text is passed as a property'
        }, {
        height: 'auto'
        }, {
        'before-close': (event) => { console.log('this will be called before the modal closes'); }
    })

.vue files can ALSO be used with Dynamic Modals:

    import MyComponent from './MyComponent.vue'

    this.$modal.show(MyComponent, {
        text: 'This text is passed as a property'
        }, {
        draggable: true
    })

Setting Default Properties for Modals in the App:

    import VueJsModal from 'plugin'

    Vue.use(VueJsModal, {
    dynamic: true,
    dynamicDefaults: {
        foo: 'foo'
    }
    })

Mutable CSS Properties
- name, draggable, scrollable, transition, classes, height/width . ..

Emittable Events
- before-open, opened, before-close, closed

## Unit Testing in Vue 2.0 using Jest

Include the plugin to your <Component>.spec.js.

For example: If you're using the plugin in your Main component, then you should include the plugin to your Main.spec.js file.

    import VModal from 'vue-js-modal'

    Vue.use(VModal)


## Actual Implementation into the frontend-scaffold
(This may be out-of-date upon reading)

In <Notes.vue>:

    <template>
    <div class="center">
        <notes-form class="form" v-on:saveNote="saveNote($event)" />
        <notes-display class="notes" :notes="notes" />
        <v-dialog/>
    </div>
    </template>

In <main.js>

    import VModal from 'vue-js-modal';
    Vue.use(VModal, { dialog: true });

In <NoteDisplay.vue>

    <template>
    ...
    <button @click="confirmDeleteNote(noteIndex)">Delete</button>
    ...
    </template>

    <script>
    ...
    confirmDeleteNote(state, noteIndex) {
        this.$modal.show('dialog', {
        title: 'Confirm',
        text: 'Are you sure you would like to delete: '+ this.note.title +'?',
        buttons: [
            {
            title: 'Yes',
            handler: () => { 
                this.deleteNote(noteIndex);
                this.$modal.hide('dialog');
            },
            },
            {
            title: '',       // Button title
            default: true,    // Will be triggered by default if 'Enter' pressed.
            handler: () => {} // Button click handler
            },
            {
            title: 'No',
            },
        ], 
        'before-close': (event) => { console.log('this will be called before the modal closes'); },
        });
    },
    ...
    </script>









