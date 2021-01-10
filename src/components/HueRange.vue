<template>
    <input
        class="range"
        type="range"
        v-model="internalValue"
        min="0"
        max="359"
        step="1"
        :style="{ '--color': color }"
    />
</template>

<script>
export default {
    props: {
        value: { type: Number, required: true },
    },
    computed: {
        internalValue: {
            get() {
                return this.value || 0;
            },
            set(value) {
                this.$emit("input", value);
            },
        },
        color() {
            return `hsl(${this.value}, 100%, 50%)`;
        },
    },
};
</script>

<style scoped lang="scss">
.range {
    --ww-range-thumb-width: 10px;
    --ww-range-thumb-height: 1.5rem;
    --ww-range-track-height: 1rem;
    --ww-range-cursor: pointer;

    &:disabled {
        --ww-range-cursor: not-allowed;
    }

    -webkit-appearance: none;
    background: transparent;
    &:focus {
        outline: none; // TODO: provide an accessible alternative
    }
    &::-webkit-slider-thumb {
        -webkit-appearance: none;
    }
    &::-ms-track {
        width: 100%;
        cursor: var(--ww-range-cursor);
        background: transparent;
        border-color: transparent;
        color: transparent;
    }

    @mixin thumb() {
        height: var(--ww-range-thumb-height);
        width: var(--ww-range-thumb-width);
        border: 1px solid white;
        cursor: var(--ww-range-cursor);
        background-color: var(--color);
        outline: 1px solid black;
    }
    @mixin track() {
        width: 100%;
        height: var(--ww-range-track-height);
        cursor: var(--ww-range-cursor);
        background: -webkit-linear-gradient(
            left,
            #f00 0%,
            #ff0 16.66%,
            #0f0 33.33%,
            #0ff 50%,
            #00f 66.66%,
            #f0f 83.33%,
            #f00 100%
        );
    }

    // Need separate line for browser compatibility
    &::-webkit-slider-thumb {
        @include thumb();
        margin-top: calc((var(--ww-range-track-height) - var(--ww-range-thumb-height)) / 2);
    }
    &::-moz-range-thumb {
        @include thumb();
        border: none;
    }
    &::-ms-thumb {
        @include thumb();
    }
    &::-webkit-slider-runnable-track {
        @include track();
    }
    &::-moz-range-track {
        @include track();
    }
    &::-ms-track {
        @include track();
    }
}
</style>
