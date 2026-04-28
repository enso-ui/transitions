<template>
    <transition
        appear
        :enter-active-class="enterClass"
        :leave-active-class="leaveClass">
        <slot/>
    </transition>
</template>

<script>
const directions = {
    up: 'Up',
    down: 'Down',
    left: 'Left',
    right: 'Right',
};

const effects = ['back', 'bounce', 'fade', 'slide', 'zoom'];

const validateDirection = direction => Object.keys(directions)
    .includes(direction.toLowerCase());

const validateEffect = effect => effects.includes(effect);

export default {
    name: 'Mirror',

    props: {
        enter: {
            type: String,
            default: null,
            validator: validateDirection,
        },
        leave: {
            type: String,
            default: null,
            validator: validateDirection,
        },
        effect: {
            type: String,
            required: true,
            validator: validateEffect,
        },
    },

    computed: {
        enterClass() {
            const direction = this.enter
                ? directions[this.enter.toLowerCase()]
                : '';

            return `animate__animated animate__${this.effect}In${direction}`;
        },
        leaveClass() {
            const direction = this.leave
                ? directions[this.leave.toLowerCase()]
                : '';

            return `animate__animated animate__${this.effect}Out${direction}`;
        },
    },

    mounted() {
        if (['back', 'slide'].includes(this.effect)
            && !this.leave && !this.enter) {
            throw Error('Missing direction for the given effect');
        }
    },
};
</script>
