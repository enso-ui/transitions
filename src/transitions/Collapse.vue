<template>
    <transition :css="false"
        @before-enter="beforeEnter"
        @enter="enter"
        @after-enter="afterEnter"
        @enter-cancelled="reset"
        @before-leave="beforeLeave"
        @leave="leave"
        @after-leave="afterLeave"
        @leave-cancelled="reset">
        <div class="collapse-transition"
            :style="style"
            v-bind="$attrs"
            v-show="show">
            <slot/>
        </div>
    </transition>
</template>

<script setup>
import { computed } from 'vue';

defineOptions({
    name: 'Collapse',
    inheritAttrs: false,
});

const props = defineProps({
    show: {
        type: Boolean,
        default: true,
    },
    duration: {
        type: Number,
        default: 333,
    },
    easing: {
        type: String,
        default: 'ease',
    },
});

const style = computed(() => ({
    transitionDuration: `${props.duration}ms`,
    transitionTimingFunction: props.easing,
}));

const reset = el => {
    el.style.height = '';
    el.style.overflow = '';
};

const beforeEnter = el => {
    el.style.height = '0';
    el.style.overflow = 'hidden';
};

const enter = (el, done) => {
    const finish = event => {
        if (event.target !== el || event.propertyName !== 'height') {
            return;
        }

        el.removeEventListener('transitionend', finish);
        done();
    };

    el.addEventListener('transitionend', finish);

    requestAnimationFrame(() => {
        el.style.height = `${el.scrollHeight}px`;
    });
};

const afterEnter = el => {
    reset(el);
};

const beforeLeave = el => {
    el.style.height = `${el.scrollHeight}px`;
    el.style.overflow = 'hidden';
};

const leave = (el, done) => {
    const finish = event => {
        if (event.target !== el || event.propertyName !== 'height') {
            return;
        }

        el.removeEventListener('transitionend', finish);
        done();
    };

    el.addEventListener('transitionend', finish);

    void el.offsetHeight;

    requestAnimationFrame(() => {
        el.style.height = '0';
    });
};

const afterLeave = el => {
    reset(el);
};
</script>

<style lang="scss">
    .collapse-transition {
        display: block;
        overflow-x: visible;
        overflow-y: hidden;
        transition-property: height;
        will-change: height;
    }
</style>
