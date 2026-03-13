<script setup lang="ts">
import { ref, watch } from 'vue';
import { useSettingsStore } from '@/stores/modules/settings';
import { useI18n } from 'vue-i18n';

const settings = useSettingsStore();
const { t } = useI18n();

const state = reactive({
    // 颜色1（HEX）
    color1: settings.colorBends.colors[0] || '#ff5c7a',
    // 颜色2（HEX）
    color2: settings.colorBends.colors[1] || '#8a5cff',
    // 颜色3（HEX）
    color3: settings.colorBends.colors[2] || '#00ffd1',
    // 旋转角度（度）
    rotation: settings.colorBends.rotation,
    // 动画速度
    speed: settings.colorBends.speed,
    // 缩放比例
    scale: settings.colorBends.scale,
    // 频率参数
    frequency: settings.colorBends.frequency,
    // 扭曲强度
    warpStrength: settings.colorBends.warpStrength,
    // 鼠标影响强度
    mouseInfluence: settings.colorBends.mouseInfluence,
    // 视差强度
    parallax: settings.colorBends.parallax,
    // 噪声强度
    noise: settings.colorBends.noise,
    // 透明叠加开关
    transparent: settings.colorBends.transparent,
});
const {
    color1,
    color2,
    color3,
    rotation,
    speed,
    scale,
    frequency,
    warpStrength,
    mouseInfluence,
    parallax,
    noise,
    transparent,
} = toRefs(state);

watch(
    () => [state.color1, state.color2, state.color3],
    () => {
        const cols = [state.color1, state.color2, state.color3].map(s =>
            s.startsWith('#') ? s : `#${s}`
        );
        settings.setBendsColors(cols);
    }
);

watch(
    () => [
        state.rotation,
        state.speed,
        state.scale,
        state.frequency,
        state.warpStrength,
        state.mouseInfluence,
        state.parallax,
        state.noise,
        state.transparent,
    ],
    () => {
        settings.setColorBends({
            rotation: state.rotation,
            speed: state.speed,
            scale: state.scale,
            frequency: state.frequency,
            warpStrength: state.warpStrength,
            mouseInfluence: state.mouseInfluence,
            parallax: state.parallax,
            noise: state.noise,
            transparent: state.transparent,
        });
    }
);

const reset = () => {
    settings.resetColorBends();
    const cols = settings.colorBends.colors;
    state.color1 = cols[0];
    state.color2 = cols[1];
    state.color3 = cols[2];
    state.rotation = settings.colorBends.rotation;
    state.speed = settings.colorBends.speed;
    state.scale = settings.colorBends.scale;
    state.frequency = settings.colorBends.frequency;
    state.warpStrength = settings.colorBends.warpStrength;
    state.mouseInfluence = settings.colorBends.mouseInfluence;
    state.parallax = settings.colorBends.parallax;
    state.noise = settings.colorBends.noise;
    state.transparent = settings.colorBends.transparent;
};

const previewStyle = computed(() => {
    const rot = Number(state.rotation) || 0;
    const scale = Number(state.scale) || 1;
    const speed = Math.max(0.05, Number(state.speed) || 0.5);
    const freq = Number(state.frequency) || 1;
    const parallax = Number(state.parallax) || 0;
    const gradient = `linear-gradient(${rot}deg, ${state.color1}, ${state.color2}, ${state.color3})`;
    const duration = (4 / speed).toFixed(2) + 's';
    return {
        backgroundImage: gradient,
        backgroundSize: '200% 200%',
        transform: `scale(${scale})`,
        animation: `cb-move ${duration} linear infinite`,
        filter: `saturate(${1 + freq / 2}) brightness(${1 + parallax / 4})`,
        opacity: state.transparent ? 0.85 : 1,
        transition:
            'background 120ms ease, transform 120ms ease, filter 120ms ease, opacity 120ms ease',
    };
});
</script>

<template>
    <div class="glass-card p-4">
        <h3 class="text-primary mb-4 text-lg font-semibold">
            {{ t('components.background.colorBends.title') }}
        </h3>

        <div class="grid grid-cols-1 gap-6 md:grid-cols-2">
            <div class="space-y-6">
                <div>
                    <label class="text-primary/80 mb-2 block text-sm">{{
                        t('components.background.common.colors3')
                    }}</label>
                    <div class="grid grid-cols-3 gap-3">
                        <div class="space-y-2">
                            <input v-model="color1" type="color" class="h-9 w-full rounded" />
                            <input
                                v-model="color1"
                                type="text"
                                class="text-primary w-full rounded bg-white/10 p-2"
                            />
                        </div>
                        <div class="space-y-2">
                            <input v-model="color2" type="color" class="h-9 w-full rounded" />
                            <input
                                v-model="color2"
                                type="text"
                                class="text-primary w-full rounded bg-white/10 p-2"
                            />
                        </div>
                        <div class="space-y-2">
                            <input v-model="color3" type="color" class="h-9 w-full rounded" />
                            <input
                                v-model="color3"
                                type="text"
                                class="text-primary w-full rounded bg-white/10 p-2"
                            />
                        </div>
                    </div>
                </div>

                <div>
                    <label class="text-primary/80 mb-2 block text-sm">{{
                        t('components.background.common.params')
                    }}</label>
                    <div class="space-y-3">
                        <div class="flex items-center gap-3">
                            <span class="text-primary/70 w-24 text-xs">{{
                                t('components.background.colorBends.rotation')
                            }}</span>
                            <input
                                v-model.number="rotation"
                                type="range"
                                min="0"
                                max="360"
                                step="1"
                                class="flex-1"
                            />
                            <input
                                v-model.number="rotation"
                                type="number"
                                min="0"
                                max="360"
                                step="1"
                                class="text-primary w-24 rounded bg-white/10 p-2"
                            />
                        </div>
                        <div class="flex items-center gap-3">
                            <span class="text-primary/70 w-24 text-xs">{{
                                t('components.background.colorBends.speed')
                            }}</span>
                            <input
                                v-model.number="speed"
                                type="range"
                                min="0.05"
                                max="3"
                                step="0.05"
                                class="flex-1"
                            />
                            <input
                                v-model.number="speed"
                                type="number"
                                min="0.05"
                                max="3"
                                step="0.05"
                                class="text-primary w-24 rounded bg-white/10 p-2"
                            />
                        </div>
                        <div class="flex items-center gap-3">
                            <span class="text-primary/70 w-24 text-xs">{{
                                t('components.background.colorBends.scale')
                            }}</span>
                            <input
                                v-model.number="scale"
                                type="range"
                                min="0.5"
                                max="3"
                                step="0.1"
                                class="flex-1"
                            />
                            <input
                                v-model.number="scale"
                                type="number"
                                min="0.5"
                                max="3"
                                step="0.1"
                                class="text-primary w-24 rounded bg-white/10 p-2"
                            />
                        </div>
                        <div class="flex items-center gap-3">
                            <span class="text-primary/70 w-24 text-xs">{{
                                t('components.background.colorBends.frequency')
                            }}</span>
                            <input
                                v-model.number="frequency"
                                type="range"
                                min="0.5"
                                max="3"
                                step="0.1"
                                class="flex-1"
                            />
                            <input
                                v-model.number="frequency"
                                type="number"
                                min="0.5"
                                max="3"
                                step="0.1"
                                class="text-primary w-24 rounded bg-white/10 p-2"
                            />
                        </div>
                        <div class="flex items-center gap-3">
                            <span class="text-primary/70 w-24 text-xs">{{
                                t('components.background.colorBends.warpStrength')
                            }}</span>
                            <input
                                v-model.number="warpStrength"
                                type="range"
                                min="0"
                                max="3"
                                step="0.1"
                                class="flex-1"
                            />
                            <input
                                v-model.number="warpStrength"
                                type="number"
                                min="0"
                                max="3"
                                step="0.1"
                                class="text-primary w-24 rounded bg-white/10 p-2"
                            />
                        </div>
                        <div class="flex items-center gap-3">
                            <span class="text-primary/70 w-24 text-xs">{{
                                t('components.background.colorBends.mouseInfluence')
                            }}</span>
                            <input
                                v-model.number="mouseInfluence"
                                type="range"
                                min="0"
                                max="2"
                                step="0.1"
                                class="flex-1"
                            />
                            <input
                                v-model.number="mouseInfluence"
                                type="number"
                                min="0"
                                max="2"
                                step="0.1"
                                class="text-primary w-24 rounded bg-white/10 p-2"
                            />
                        </div>
                        <div class="flex items-center gap-3">
                            <span class="text-primary/70 w-24 text-xs">{{
                                t('components.background.colorBends.parallax')
                            }}</span>
                            <input
                                v-model.number="parallax"
                                type="range"
                                min="0"
                                max="2"
                                step="0.1"
                                class="flex-1"
                            />
                            <input
                                v-model.number="parallax"
                                type="number"
                                min="0"
                                max="2"
                                step="0.1"
                                class="text-primary w-24 rounded bg-white/10 p-2"
                            />
                        </div>
                        <div class="flex items-center gap-3">
                            <span class="text-primary/70 w-24 text-xs">{{
                                t('components.background.colorBends.noise')
                            }}</span>
                            <input
                                v-model.number="noise"
                                type="range"
                                min="0"
                                max="0.5"
                                step="0.01"
                                class="flex-1"
                            />
                            <input
                                v-model.number="noise"
                                type="number"
                                min="0"
                                max="0.5"
                                step="0.01"
                                class="text-primary w-24 rounded bg-white/10 p-2"
                            />
                        </div>
                        <div class="flex items-center justify-between">
                            <label for="trans" class="text-primary/70 text-xs">{{
                                t('components.background.colorBends.transparent')
                            }}</label>
                            <input
                                id="trans"
                                v-model="transparent"
                                type="checkbox"
                                class="h-4 w-4"
                            />
                        </div>
                    </div>
                </div>

                <div class="flex items-center justify-between">
                    <p class="text-primary/60 text-xs">
                        {{ t('components.background.colorBends.tip') }}
                    </p>
                    <button
                        class="text-primary rounded bg-white/10 px-3 py-2 hover:bg-white/20"
                        @click="reset"
                    >
                        {{ t('components.background.common.reset') }}
                    </button>
                </div>
            </div>

            <div>
                <label class="text-primary/80 mb-2 block text-sm">{{
                    t('components.background.common.preview')
                }}</label>
                <div class="relative overflow-hidden rounded-lg border border-white/10 bg-black/20">
                    <div class="cb-preview aspect-video w-full" :style="previewStyle"></div>
                    <div
                        class="text-primary/80 absolute inset-x-0 bottom-0 flex items-center justify-between gap-3 bg-black/30 px-3 py-2 text-xs"
                    >
                        <span>{{ color1 }} · {{ color2 }} · {{ color3 }}</span>
                        <span
                            >{{ t('components.background.colorBends.rotation') }} {{ rotation }} /
                            {{ t('components.background.colorBends.scale') }} {{ scale }} /
                            {{ t('components.background.colorBends.speed') }} {{ speed }}</span
                        >
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
@keyframes cb-move {
    0% {
        background-position: 0% 50%;
    }
    50% {
        background-position: 100% 50%;
    }
    100% {
        background-position: 0% 50%;
    }
}
.cb-preview {
    will-change: background-position, transform, filter, opacity;
}
</style>
