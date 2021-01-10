<template>
    <div
        :style="[{ backgroundColor: color }, position]"
        class="relative border border-black main"
        @mouseup="stopDrag"
        @mouseleave="stopDrag"
        @mousemove="onMouseMove"
    >
        <div
            class="absolute w-4 h-4 border border-white rounded-full cursor-pointer cursor"
            @mousedown="startDrag"
        ></div>
    </div>
</template>

<script>
export default {
    props: {
        hslValues: { type: Array, required: true },
    },
    data() {
        return {
            isDraging: false,
        };
    },
    methods: {
        startDrag() {
            this.isDraging = true;
            this.elementBoudingClientRect = this.$el.getBoundingClientRect();
        },
        stopDrag() {
            this.isDraging = false;
        },
        onMouseMove($event) {
            if (!this.isDraging) return;
            const { width, left, bottom, height } = this.elementBoudingClientRect;
            const s = parseInt((($event.clientX - left) / width) * 100);
            const l = parseInt(((bottom - $event.clientY) / height) * 100);

            this.$emit("input", [this.hslValues[0], s, l, this.hslValues[3]]);
        },
    },
    computed: {
        color() {
            return `hsl(${this.hslValues[0]}, 100%, 50% )`;
        },
        position() {
            return {
                "--x": `${this.hslValues[1]}%`,
                "--y": `${this.hslValues[2]}%`,
            };
        },
    },
};
</script>

<style scoped lang="scss">
.main {
    background: -webkit-linear-gradient(
            top,
            hsl(0, 0%, 100%) 0%,
            hsla(0, 0%, 100%, 0) 50%,
            hsla(0, 0%, 0%, 0) 50%,
            hsl(0, 0%, 0%) 100%
        ),
        -webkit-linear-gradient(left, hsl(0, 0%, 50%) 0%, hsla(0, 0%, 50%, 0) 100%);
}
.cursor {
    box-shadow: 1px 1px black;
    bottom: var(--y);
    left: var(--x);
    transform: translate(-50%, 50%);
    &.cursor-none {
        cursor: none;
    }
}
</style>
