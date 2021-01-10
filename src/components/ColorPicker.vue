<template>
    <div class="p-3">
        <div class="flex h-24">
            <div :style="{ backgroundColor: value }" class="w-24 h-full border border-black"></div>
            <ColorPickerDrag :hslValues="hslValues" @input="setHSL" class="flex-1 h-full"></ColorPickerDrag>
        </div>
        <div class="mt-3">
            <HueRange class="w-full" :value="hslValues[0]" @input="setHue"></HueRange>
            <AlphaRange
                class="w-full mt-1"
                :color="colorWithoutAlpha"
                :value="hslValues[3] || 1"
                @input="setAlpha"
            ></AlphaRange>
        </div>
        <div class="mt-3">
            <select :value="format" @input="setFormat($event.target.value)">
                <option value="hsl">HSL</option>
                <option value="rgb">RGB</option>
                <option value="hex">Hexadecimal</option>
            </select>
        </div>
        <div class="mt-2">
            <template v-if="format === 'hex'">
                <input
                    type="text"
                    :value="value"
                    @input="setHex($event.target.value)"
                    @blur="$event.target.value = value"
                />
                <input
                    type="number"
                    @blur="$event.target.value = hsValues[3]"
                    :value="hslValues[3] * 100"
                    @input="setAlpha($event.target.value / 100)"
                    step="1"
                    min="0"
                    max="100"
                />
            </template>
            <div class="flex" v-else-if="format === 'rgb'">
                <input
                    type="number"
                    step="1"
                    min="0"
                    max="255"
                    :value="rgbValues[0]"
                    @input="setRGB([$event.target.value, rgbValues[1], rgbValues[2], rgbValues[3]])"
                    class="flex-1 min-w-0 mx-2"
                />
                <input
                    type="number"
                    step="1"
                    min="0"
                    max="255"
                    :value="rgbValues[1]"
                    @input="setRGB([rgbValues[0], $event.target.value, rgbValues[2], rgbValues[3]])"
                    class="flex-1 min-w-0 mx-2"
                />
                <input
                    type="number"
                    step="1"
                    min="0"
                    max="255"
                    :value="rgbValues[2]"
                    @input="setRGB([rgbValues[0], rgbValues[1], $event.target.value, rgbValues[3]])"
                    class="flex-1 min-w-0 mx-2"
                />
                <input
                    type="number"
                    @blur="$event.target.value = hslValues[3]"
                    :value="hslValues[3] * 100"
                    @input="setAlpha($event.target.value / 100)"
                    step="1"
                    min="0"
                    max="100"
                    class="flex-1 min-w-0 mx-2"
                />
            </div>
            <div class="flex" v-else-if="format === 'hsl'">
                <input
                    type="number"
                    step="1"
                    min="0"
                    max="255"
                    :value="hslValues[0]"
                    @input="setHSL([$event.target.value, hslValues[1], hslValues[2], hslValues[3]])"
                    class="flex-1 min-w-0 mx-2"
                />
                <input
                    type="number"
                    step="1"
                    min="0"
                    max="255"
                    :value="hslValues[1]"
                    @input="setHSL([hslValues[0], $event.target.value, hslValues[2], hslValues[3]])"
                    class="flex-1 min-w-0 mx-2"
                />
                <input
                    type="number"
                    step="1"
                    min="0"
                    max="255"
                    :value="hslValues[2]"
                    @input="setHSL([hslValues[0], hslValues[1], $event.target.value, hslValues[3]])"
                    class="flex-1 min-w-0 mx-2"
                />
                <input
                    type="number"
                    @blur="$event.target.value = hslValues[3]"
                    :value="hslValues[3] * 100"
                    @input="setAlpha($event.target.value / 100)"
                    step="1"
                    min="0"
                    max="100"
                    class="flex-1 min-w-0 mx-2"
                />
            </div>
        </div>
    </div>
</template>

<script>
import colorString from "color-string";

import ColorPickerDrag from "./ColorPickerDrag";
import HueRange from "./HueRange";
import AlphaRange from "./AlphaRange";

export default {
    components: { ColorPickerDrag, HueRange, AlphaRange },
    props: {
        value: { type: String, default: "#000000" },
    },
    data() {
        return {
            hslValues: [],
            rgbValues: [],
            format: "hsl",
        };
    },
    computed: {
        colorWithoutAlpha() {
            return colorString.to.hsl(this.hslValues.slice(0, 3));
        },
    },
    methods: {
        getStringValueFromHsl(hsl, format = this.format) {
            if (format === "hsl") {
                if (hsl[3] == 1) {
                    hsl = hsl.slice(0, 3)
                }
                return colorString.to.hsl(hsl);
            }
            let rgb = this.convertHSLToRGB(hsl);
            if (rgb[3] !== undefined) {
                rgb[3] = parseFloat(rgb[3]).toFixed(2);
            }
            if (rgb[3] == 1) {
                rgb = rgb.slice(0, 3)
            }
            return format === "hex" ? colorString.to.hex(rgb) : colorString.to.rgb(rgb);
        },
        setHue(hue) {
            const color = [...this.hslValues];
            color[0] = hue;
            this.$emit("input", this.getStringValueFromHsl(color));
        },
        setAlpha(alpha) {
            const color = [...this.hslValues];
            color[3] = parseFloat(alpha);
            this.$emit("input", this.getStringValueFromHsl(color));
        },
        setFormat(format) {
            this.$emit("input", this.getStringValueFromHsl(this.hslValues, format));
        },
        setHex(hex) {
            if (hex === this.value) return;
            const values = colorString.get.rgb(hex);
            if (values) {
                values[3] = values[3] === undefined ? 1 : parseFloat(values[3].toFixed(2));
                this.$emit("input", colorString.to.hex(values));
            }
        },
        setRGB(rgb) {
            rgb[3] = rgb[3] === undefined ? 1 : parseFloat(rgb[3].toFixed(2));
            this.$emit("input", colorString.to.rgb(rgb));
        },
        setHSL(hsl) {
            hsl[3] = hsl[3] === undefined ? 1 : parseFloat(hsl[3].toFixed(2));
            this.$emit("input", colorString.to.hsl(hsl));
        },

        // Thanks to https://css-tricks.com/converting-color-spaces-in-javascript/ for the formula
        convertRGBToHSL([r, g, b, alpha = 1]) {
            r /= 255;
            g /= 255;
            b /= 255;

            let cmin = Math.min(r, g, b);
            let cmax = Math.max(r, g, b);
            let delta = cmax - cmin;
            let h = 0;
            let s = 0;
            let l = 0;

            if (delta == 0) h = 0;
            // Red is max
            else if (cmax == r) h = ((g - b) / delta) % 6;
            // Green is max
            else if (cmax == g) h = (b - r) / delta + 2;
            // Blue is max
            else h = (r - g) / delta + 4;

            h = Math.round(h * 60);

            // Make negative hues positive behind 360°
            if (h < 0) h += 360;

            l = (cmax + cmin) / 2;
            s = delta == 0 ? 0 : delta / (1 - Math.abs(2 * l - 1));
            s = +(s * 100).toFixed(0);
            l = +(l * 100).toFixed(0);

            return [h, s, l, alpha];
        },

        // Thanks to https://css-tricks.com/converting-color-spaces-in-javascript/ for the formula
        convertHSLToRGB([h, s, l, alpha = 1]) {
            s /= 100;
            l /= 100;

            let c = (1 - Math.abs(2 * l - 1)) * s;
            let x = c * (1 - Math.abs(((h / 60) % 2) - 1));
            let m = l - c / 2;
            let r = 0;
            let g = 0;
            let b = 0;

            if (0 <= h && h < 60) {
                r = c;
                g = x;
                b = 0;
            } else if (60 <= h && h < 120) {
                r = x;
                g = c;
                b = 0;
            } else if (120 <= h && h < 180) {
                r = 0;
                g = c;
                b = x;
            } else if (180 <= h && h < 240) {
                r = 0;
                g = x;
                b = c;
            } else if (240 <= h && h < 300) {
                r = x;
                g = 0;
                b = c;
            } else if (300 <= h && h < 360) {
                r = c;
                g = 0;
                b = x;
            }
            r = Math.round((r + m) * 255);
            g = Math.round((g + m) * 255);
            b = Math.round((b + m) * 255);

            return [r, g, b, alpha];
        },
    },
    watch: {
        value: {
            immediate: true,
            handler(value, oldValue) {
                if (value === oldValue) return;
                const { model, value: values } = colorString.get(value) || {};
                if (model === "rgb") {
                    this.rgbValues = values;
                    this.hslValues = this.convertRGBToHSL(values);
                    this.format = value.startsWith("#") ? "hex" : "rgb";
                } else if (model === "hsl") {
                    this.format = model;
                    this.hslValues = values;
                    this.rgbValues = this.convertHSLToRGB(values);
                }
                if (this.hslValues[3] !== undefined) {
                    this.hslValues[3] = parseFloat(this.hslValues[3].toFixed(2));
                } else {
                    this.hslValues[3] = parseFloat(this.hslValues[3].toFixed(2));
                }
            },
        },
    },
};
</script>
