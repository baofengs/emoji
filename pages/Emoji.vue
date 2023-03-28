<style lang="scss">
.cmp-emoji {
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    z-index: 999;
    background-color: #fff;

    &--show {
            animation: popup .25s cubic-bezier(0.075, 0.82, 0.165, 1);
        }

    &-bg {
        position: fixed;
        left: 0;
        right: 0;
        bottom: 0;
        top: 0;
        z-index: 99;
        background-color: rgba(39, 31, 31, 0.5);
    }

    &-catigaries {
        position: relative;
        display: flex;
        align-items: center;
        justify-content: center;
        padding: .5em 0 .75em;

        &-anchor {
            position: absolute;
            bottom: .15em;
            left: 60px;
            width: 1em;
            height: .15em;
            border-radius: 99px;
            background-color: black;
            transition: all .125s cubic-bezier(0.075, 0.82, 0.165, 1);
        }
    }

    &-catigary {
        width: 2.5em;
        text-align: center;
    }

    &-list {
        display: grid;
        grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr 1fr 1fr;
        height: 100%;
        margin: 0 .75em;
    }

    &-item {
        display: inline-flex;
        justify-content: center;
        align-items: center;
        width: 2em;
        height: 2em;
        border: 1px solid transparent;
        border-radius: 12px;
        font-size: 1.25em;

        &--active {
            border-color: red;
        }
    }

    &-swiper {
        display: flex;
        align-items: center;
        justify-content: center;
        height: 55vh;
        padding-bottom: calc(1em + constant(safe-area-inset-bottom));
        padding-bottom: calc(1em + env(safe-area-inset-bottom));

        &-item {
            height: 100%;
            overflow-y: scroll;
        }
        
    }
}

@keyframes popup {
    from {
        transform: translateY(100%);
        opacity: 0;
    }
    to {
        transform: translateY(0);
        opacity: 1;
    }
}
</style>

<template>
    <view :class="['cmp-emoji', {'cmp-emoji--show': show}]" v-if="show">
        <view class="cmp-emoji-catigaries">
            <template v-for="item of catigaries" :key="item">
                <view class="cmp-emoji-catigary" @click="onChangeCatigary(item)">{{ item }}</view>
            </template>
            <view class="cmp-emoji-catigaries-anchor" :style="anchorStyle"></view>
        </view>
        <swiper class="cmp-emoji-swiper" @change="onChageSwiper" :current="currentSwiper">
            <template v-for="key of catigaries" :key="key">
                <swiper-item class="cmp-emoji-swiper-item">
                    <view class="cmp-emoji-list">
                        <template v-for="emoji of emojiList[key]" :key="emoji">
                            <view :class="['cmp-emoji-item', { 'cmp-emoji-item--active': currentEmoji === emoji }]"
                                @click="onSelect(emoji)">{{ emoji }}</view>
                        </template>
                    </view>
                </swiper-item>
            </template>
        </swiper>
    </view>
    <view class="cmp-emoji-bg" @click="onClose" v-if="show"></view>
</template>


<script setup>
import { emojiList } from './emojis';
import { ref, computed, defineProps, defineEmits, watch } from 'vue'

const catigary = ref('😃');
const catigaries = computed(() => Object.keys(emojiList));

const currentSwiper = ref(0);

const currentEmoji = ref(props.emoji);
const translateX = ref(0);
const anchorStyle = computed(() => `transform: translateX(${translateX.value}px)`);

const props = defineProps({
    show: false,
    emoji: ''
});

watch(() => props.emoji, emoji => {
    currentEmoji.value = emoji;
});

watch(() => props.show, show => {
    if (show) {
        Object.keys(emojiList).forEach(key => {
            const emojis = emojiList[key];
            if (emojis.indexOf(currentEmoji.value) > -1) {
                catigary.value = key;
                currentSwiper.value = catigaries.value.indexOf(key);
            }
        });
    }
});

watch(() => catigary.value, catigary => {
    const index = catigaries.value.indexOf(catigary);
    translateX.value = index * 40;
    currentSwiper.value = index;
});

const emits = defineEmits(['close', 'select', 'confirm']);

function onSelect(emoji) {
    currentEmoji.value = emoji;
    emits('select', emoji);
}

function onClose() {
    emits('close');
}

function onChangeCatigary(item) {
    const current = catigaries.value.indexOf(item);
    currentSwiper.value = current;
    catigary.value = item;
}

function onChageSwiper(e) {
    const { current } = e.detail;
    catigary.value = catigaries.value[current];
}

</script>
