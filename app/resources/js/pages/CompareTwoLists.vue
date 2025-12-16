<template>
    <Head title="Compare Two Lists">
        <link rel="preconnect" href="https://rsms.me/" />
        <link rel="stylesheet" href="https://rsms.me/inter/inter.css" />
    </Head>
    <div>
        <section class="p-4 bg-green-800 text-green-500 text-2xl">
            Compare Two Lists
        </section>

        <section class="p-4">
            <div class="flex gap-4">
                <div class="flex-1">
                    <p class="text-xl mb-2">Primary list</p>
                    <input v-model="primarySeparator" type="text" name="primary-separator" class="w-full p-2 border-1 rounded-sm mb-4" placeholder="Primary separator" />
                    <textarea v-model="primaryData" class="w-full p-2 border-1 rounded-sm" placeholder="Paste your primary text" rows="15"></textarea>
                </div>
                <div class="flex-1">
                    <p class="text-xl mb-2">Secondary list</p>
                    <input v-model="secondarySeparator" type="text" name="secondary-separator" class="w-full p-2 border-1 rounded-sm mb-4" placeholder="Primary separator" />
                    <textarea v-model="secondaryData" class="w-full p-2 border-1 rounded-sm" placeholder="Paste your primary text" rows="15"></textarea>
                </div>
            </div>
            <div class="my-8">
                <p class="text-xl mb-2">Set options for both lists</p>

                <ul>
                    <li>
                        <label class="cursor-pointer select-none">
                            <input v-model="options.trim" type="checkbox" name="trim" />
                            Trim data before compare
                        </label>
                    </li>
                    <li>
                        <label class="cursor-pointer select-none">
                            <input v-model="options.removeEmpty" type="checkbox" name="trim" />
                            Remove empty before compare
                        </label>
                    </li>
                    <li>
                        <label class="cursor-pointer select-none">
                            <input v-model="options.removeDuplicates" type="checkbox" name="trim" />
                            Remove duplicates before compare
                        </label>
                    </li>
                </ul>
            </div>
            <div class="text-center my-8">
                <span class="px-6 py-3 bg-green-400 text-green-800 font-bold cursor-pointer rounded-sm hover:bg-green-500 active:bg-green-600 select-none" @click="prepareLists">Compare data</span>
            </div>
        </section>

        <hr class="m-4" />

        <section class="p-4">
            <div class="text-center">

                <div class="grid grid-cols-3 gap-4 ">
                    <div class="flex-1">
                        <p class="text-xl mb-2">Only on primary</p>
                    </div>
                    <div class="flex-1">
                        <p class="text-xl mb-2">Both</p>
                    </div>
                    <div class="flex-1">
                        <p class="text-xl mb-2">Only on secondary</p>
                    </div>
                </div>

                <template v-for="data in preparedData">
                    <div class="grid grid-cols-3 gap-4 mb-1 hover:bg-gray-100">
                        <div class="h-[24px]">
                            {{ data.source === 'P' && ! data.both ? data.value : '' }}
                        </div>
                        <div class="h-[24px]">
                            {{ data.both ? data.value : '' }}
                        </div>
                        <div class="h-[24px]">
                            {{ data.source === 'S' && ! data.both ? data.value : '' }}
                        </div>
                    </div>
                </template>
            </div>
        </section>
    </div>
</template>

<script setup lang="ts">
import { Head } from '@inertiajs/vue3';
import { ref } from 'vue'; 


const primaryData = ref<string>('111;222;333;444;555;666;777;888;999;222;');
const secondaryData = ref<string>('000;444;888;121;232;343;888');

const primarySeparator = ref<string>(';');
const secondarySeparator = ref<string>(';');

const primaryList = ref<string[]>([]);
const secondaryList = ref<string[]>([]);

const preparedData = ref<{ value: string; source: string; both: boolean }[]>([]);

const options = ref<{ trim: boolean; removeDuplicates: boolean; removeEmpty: boolean }>({
    trim: false,
    removeDuplicates: false,
    removeEmpty: false,
});

const prepareLists = () => {
    preparedData.value = [];

    primaryList.value = primaryData.value.split(primarySeparator.value);
    secondaryList.value = secondaryData.value.split(secondarySeparator.value);

    trimListsData();
    removeEmpty();
    removeDuplicates();

    for (let index in primaryList.value) {
        compare(true, index);
    }

    for (let index in secondaryList.value) {
        compare(false, index);
    }
};

const removeDuplicates = () => {
    if (! options.value.removeDuplicates) {
        return;
    }

    primaryList.value = [...new Set(primaryList.value)];
    secondaryList.value = [...new Set(secondaryList.value)];
};

const removeEmpty = () => {
    if (! options.value.removeEmpty) {
        return;
    }

    primaryList.value = primaryList.value.filter(v => v.trim() !== "");
    secondaryList.value = secondaryList.value.filter(v => v.trim() !== "");
};

const trimListsData = () => {
    if (! options.value.trim) {
        return;
    }

    primaryList.value = primaryList.value.map(v => v.trim());
    secondaryList.value = secondaryList.value.map(v => v.trim());
};

const compare = (isPrimary: boolean, index: any) => {
    const current = isPrimary ? 'primary' : 'secondary';
    const other = isPrimary ? 'secondary' : 'primary';

    const value = isPrimary 
                    ? primaryList.value[index]
                    : secondaryList.value[index];

    const exists = isPrimary 
                    ? secondaryList.value.includes(value)
                    : primaryList.value.includes(value);

    preparedData.value.push({
        value: value,
        source: isPrimary ? 'P' : 'S',
        both: exists,
    });
};
</script>
