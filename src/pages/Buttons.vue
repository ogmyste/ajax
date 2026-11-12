<script setup>
import { ref } from 'vue';
import Button from '../components/Button.vue';

let clicks = ref(0);
let showInvalid = ref(false);
let submitted = ref('');

function submit() {
    submitted.value = 'Form submitted at ' + new Date().toLocaleTimeString();
}

let snippet = `<Button>Default</Button>
<Button color="primary" size="large">Save</Button>
<Button color="danger" outlined rounded>Delete</Button>
<Button color="link" light :loading="saving">Loading…</Button>
<Button disabled>Disabled</Button>`;
</script>
<template>
    <div class="container">
        <h1 class="title">Button</h1>
        <p class="subtitle">Bulma's <code>.button</code> wrapped in a Vue component with validated props.</p>

        <section class="box">
            <h2 class="title is-4">Colors</h2>
            <p class="subtitle is-6">The <code>color</code> prop accepts any Bulma color modifier.</p>
            <div class="buttons">
                <Button>Default</Button>
                <Button color="white">White</Button>
                <Button color="light">Light</Button>
                <Button color="dark">Dark</Button>
                <Button color="black">Black</Button>
                <Button color="text">Text</Button>
                <Button color="primary">Primary</Button>
                <Button color="link">Link</Button>
                <Button color="info">Info</Button>
                <Button color="success">Success</Button>
                <Button color="warning">Warning</Button>
                <Button color="danger">Danger</Button>
            </div>
        </section>

        <section class="box">
            <h2 class="title is-4">Sizes</h2>
            <p class="subtitle is-6">The <code>size</code> prop: small, normal (default), medium, large.</p>
            <div class="buttons is-align-items-center">
                <Button color="primary" size="small">Small</Button>
                <Button color="primary">Normal</Button>
                <Button color="primary" size="medium">Medium</Button>
                <Button color="primary" size="large">Large</Button>
            </div>
        </section>

        <section class="box">
            <h2 class="title is-4">Modifiers</h2>
            <p class="subtitle is-6">Boolean props map to Bulma modifiers: outlined, rounded, light, inverted, fullwidth.</p>
            <div class="buttons">
                <Button color="primary" outlined>Outlined</Button>
                <Button color="primary" rounded>Rounded</Button>
                <Button color="info" light>Light</Button>
                <Button color="success" inverted>Inverted</Button>
                <Button color="danger" outlined rounded light>Outlined + rounded + light</Button>
            </div>
            <Button color="link" fullwidth>Fullwidth</Button>
        </section>

        <section class="box">
            <h2 class="title is-4">States</h2>
            <p class="subtitle is-6"><code>loading</code> and <code>disabled</code> block clicks and add the native disabled attribute; <code>static</code> only removes the hover/press effect.</p>
            <div class="buttons">
                <Button color="primary" loading>Loading</Button>
                <Button color="link" loading outlined>Loading outlined</Button>
                <Button color="primary" disabled>Disabled</Button>
                <Button static>Static</Button>
            </div>
        </section>

        <section class="box">
            <h2 class="title is-4">Usage &amp; events</h2>
            <p class="subtitle is-6">Every click is emitted as a Vue <code>click</code> event with the native event object.</p>
            <div class="buttons is-align-items-center">
                <Button color="success" @click="clicks++">Click me</Button>
                <Button color="warning" outlined @click="clicks = 0">Reset counter</Button>
                <span class="tag is-medium is-light">Clicks: {{ clicks }}</span>
            </div>

            <hr>

            <form @submit.prevent="submit">
                <div class="field is-grouped">
                    <div class="control is-expanded">
                        <input class="input" type="text" placeholder="type='submit' inside a form">
                    </div>
                    <div class="control">
                        <Button type="submit" color="primary">Submit</Button>
                    </div>
                    <div class="control">
                        <Button type="reset" color="light">Reset</Button>
                    </div>
                </div>
            </form>
            <p v-if="submitted" class="help is-success">{{ submitted }}</p>
        </section>

        <section class="box">
            <h2 class="title is-4">Prop validation</h2>
            <p class="subtitle is-6">
                Invalid values are rejected by the prop validators and Vue prints a warning in the console.
            </p>
            <Button color="dark" @click="showInvalid = !showInvalid">
                {{ showInvalid ? 'Hide' : 'Show' }} invalid button
            </Button>
            <div v-if="showInvalid" class="mt-4">
                <Button color="bla" size="bla">color="bla" size="bla"</Button>
                <p class="help is-danger mt-2">
                    Open the browser console: <code>Invalid prop: custom validator check failed for prop "color"</code>.
                </p>
            </div>
        </section>

        <section class="box">
            <h2 class="title is-4">Template</h2>
            <pre>{{ snippet }}</pre>
        </section>
    </div>
</template>
