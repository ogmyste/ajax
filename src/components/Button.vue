<script setup>
import { computed } from 'vue';

let props = defineProps({
    color: {
        type: String,
        // Bulma 1.0 color modifiers
        validator(value) {
            return [
                'white',
                'light',
                'dark',
                'black',
                'text',
                'primary',
                'link',
                'info',
                'success',
                'warning',
                'danger'
            ].includes(value);
        }
    },
    size: {
        type: String,
        validator(value) {
            return [
                'small',
                'normal',
                'medium',
                'large'
            ].includes(value);
        },
        default: 'normal'
    },
    type: {
        type: String,
        validator(value) {
            return ['button', 'submit', 'reset'].includes(value);
        },
        default: 'button'
    },
    outlined: {
        type: Boolean,
        default: false
    },
    rounded: {
        type: Boolean,
        default: false
    },
    light: {
        type: Boolean,
        default: false
    },
    inverted: {
        type: Boolean,
        default: false
    },
    fullwidth: {
        type: Boolean,
        default: false
    },
    loading: {
        type: Boolean,
        default: false
    },
    disabled: {
        type: Boolean,
        default: false
    },
    static: {
        type: Boolean,
        default: false
    }
});

const emit = defineEmits(['click']);

let classes = computed(() => [
    'button',
    props.color ? 'is-' + props.color : null,
    props.size !== 'normal' ? 'is-' + props.size : null,
    props.outlined ? 'is-outlined' : null,
    props.rounded ? 'is-rounded' : null,
    props.light ? 'is-light' : null,
    props.inverted ? 'is-inverted' : null,
    props.fullwidth ? 'is-fullwidth' : null,
    props.loading ? 'is-loading' : null,
    props.static ? 'is-static' : null,
].filter(Boolean));

let isInactive = computed(() => props.disabled || props.loading || props.static);

function onClick(event) {
    if (isInactive.value) {
        event.preventDefault();
        return;
    }
    emit('click', event);
}
</script>
<template>
    <button :type="type" :class="classes" :disabled="disabled || loading" :aria-busy="loading || undefined"
        @click="onClick">
        <slot></slot>
    </button>
</template>
