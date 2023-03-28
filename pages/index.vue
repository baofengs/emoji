<template>
	<view class="content">
		<view class="emoji" @click="onShow">{{ emoji }}</view>
		<Emoji :show="showEmoji" :emoji="emoji" @close="onClose" @select="onSelect"></Emoji>
	</view>
</template>

<script setup>
import Emoji from './Emoji';
import { ref } from 'vue';

const emoji = ref('');
const showEmoji = ref(false);

function onClose() {
	showEmoji.value = false;
}

function onSelect(selectedEmoji) {
	emoji.value = selectedEmoji;
	showEmoji.value = false;
}

function onShow() {
	showEmoji.value = true;
}
</script>

<style>
.content {
	display: flex;
	flex-direction: column;
	align-items: center;
}
.emoji {
	display: flex;
	align-items: center;
	justify-content: center;
	width: 3em;
	height: 3em;
	margin-top: 1em;
	border: 1px solid;
	border-radius: 12px;
	font-size: 2em;
}
</style>
