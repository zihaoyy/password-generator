<script setup>
import { reactive } from "@vue/reactivity";
import { onBeforeUnmount } from "@vue/runtime-core";
import useClipboard from "vue-clipboard3";

const { toClipboard } = useClipboard();
const setting = reactive({
  length: 16,
  lower: true,
  upper: true,
  number: true,
  symbol: true,
  showCopy: false,
  finalVal: "",
  copied: "复制",
  timer: null,
  url: "https://github.com/zihaoyy/password-generator",
});

const getLower = () => String.fromCharCode(Math.floor(Math.random() * 26) + 97);
const getUpper = () => String.fromCharCode(Math.floor(Math.random() * 26) + 65);
const getNumber = () =>
  String.fromCharCode(Math.floor(Math.random() * 10) + 48);
const getSymbol = () => {
  const symbols = '~!@#$%^&*()_+{}":?><;.,';
  return symbols[Math.floor(Math.random() * symbols.length)];
};

const randomFunc = {
  lower: getLower,
  upper: getUpper,
  number: getNumber,
  symbol: getSymbol,
};

let generate = () => {
  setting.showCopy = true;
  let { lower, upper, number, symbol, length } = setting;
  let generatedPassword = "";
  const typesCount = lower + upper + number + symbol;
  if (typesCount === 0) {
    setting.finalVal = "请至少设置一项";
    return;
  }
  const typesArr = [{ lower }, { upper }, { number }, { symbol }].filter(
    (item) => Object.values(item)[0]
  );
  if (typesCount === 0) return "";
  for (let i = 0; i < length; i++) {
    typesArr.forEach((type) => {
      const funcName = Object.keys(type)[0];
      generatedPassword += randomFunc[funcName]();
    });
  }
  setting.finalVal = generatedPassword.slice(0, length);
};

let copyText = async () => {
  try {
    await toClipboard(setting.finalVal);
    setting.copied = "🟢";
  } catch (error) {
    setting.copied = "🔴";
    console.log(error);
  }
  setting.timer = setTimeout(() => (setting.copied = "复制"), 1000);
};

onBeforeUnmount(() => clearTimeout(setting.timer));
</script>

<template>
  <div class="w-96 px-6 py-4 bg-gray-900">
    <h2 class="text-white text-2xl mb-5 flex justify-between">
      <span>密码生成器</span>
      <a :href="setting.url" target="_blank" class="text-xs self-end">
        @Zihao
      </a>
    </h2>
    <div
      class="bg-slate-400 bg-opacity-10 h-14 flex items-center justify-center text-white rounded-md relative"
    >
      <span class="text-base">
        {{ setting.finalVal ? setting.finalVal : "点击下方按钮生成" }}
      </span>
      <button
        class="absolute right-0 bottom-0 blur-0 text-slate-400 text-xs px-2 py-1 bg-white bg-opacity-5 rounded-md"
        @click="copyText"
        v-if="setting.showCopy"
      >
        {{ setting.copied }}
      </button>
    </div>
    <div class="mt-4 ml-2 text-white text-xs tracking-widest">长度</div>
    <div
      class="flex items-center bg-slate-400 bg-opacity-10 h-14 cursor-pointer rounded-md mt-1"
    >
      <span class="text-white mx-5">4</span>
      <el-slider v-model="setting.length" :min="4" :max="32" />
      <span class="text-white mx-5">32</span>
    </div>
    <div class="mt-4 ml-2 text-white text-xs tracking-widest">设置</div>
    <ul>
      <li
        class="bg-slate-400 bg-opacity-10 h-14 w-full flex justify-between px-4 items-center rounded-md mt-2"
      >
        <span class="text-white tracking-wider text-base"> 小写 </span>
        <el-switch v-model="setting.lower" size="large" width="45px" />
      </li>
      <li
        class="bg-slate-400 bg-opacity-10 h-14 w-full flex justify-between px-4 items-center rounded-md mt-2"
      >
        <span class="text-white tracking-wider text-base"> 大写 </span>
        <el-switch v-model="setting.upper" size="large" width="45px" />
      </li>
      <li
        class="bg-slate-400 bg-opacity-10 h-14 w-full flex justify-between px-4 items-center rounded-md mt-2"
      >
        <span class="text-white tracking-wider text-base"> 数字 </span>
        <el-switch v-model="setting.number" size="large" width="45px" />
      </li>
      <li
        class="bg-slate-400 bg-opacity-10 h-14 w-full flex justify-between px-4 items-center rounded-md mt-2"
      >
        <span class="text-white tracking-wider text-base"> 符号 </span>
        <el-switch v-model="setting.symbol" size="large" width="45px" />
      </li>
    </ul>
    <button
      class="w-full h-12 rounded-lg mt-6 text-center bg-gradient-to-r from-cyan-500 to-blue-500 font-bold tracking-widest text-white text-base"
      @click="generate"
    >
      生成
    </button>
  </div>
</template>
