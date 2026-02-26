<template>
    <Head title="Compare Two Lists">
        <link rel="preconnect" href="https://rsms.me/" />
        <link rel="stylesheet" href="https://rsms.me/inter/inter.css" />
    </Head>
    <div>
        <section class="flex p-4 bg-green-800 text-green-500 items-center">
            <div class="flex-1 text-2xl">Compare Two Lists</div>
            <div class="text-md"><a href="https://kierzkowski.net">Go to author site</a></div>
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
                            Remove duplicates before compare (cleaning tables of duplicates)
                        </label>
                    </li>
                    <li>
                        <label class="cursor-pointer select-none">
                            <input v-model="options.removeDuplicatesAfter" type="checkbox" name="trim" />
                            Remove duplicates after compare (cleaning the results board of duplicates)
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

            <div v-if="preparedData.length === 0" class="text-center p-8 text-gray-400">
                <svg class="w-6 h-6 inline relative top-[-2px]" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="none" viewBox="0 0 24 24">
                    <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 11h2v5m-2 0h4m-2.592-8.5h.01M21 12a9 9 0 1 1-18 0 9 9 0 0 1 18 0Z"/>
                </svg>
                Click "Compare data" to show results
            </div>
        </section>

        <hr class="m-4 opacity-20" />

        <section class="p-4 bg-green-200 text-green-800 text-center border-t-2 border-t-green-800">
            © 2026 Sylwester Kierzkowski
            <br />
            Designed and developed by Sylwester Kierzkowski. This project is open for copying, modification, and use for any purpose.
            <br />
            Go to my portfolio: <a href="https://kierzkowski.net" target="blank">kierzkowski.net</a>
        </section>
    </div>
</template>

<script setup lang="ts">
import { Head } from '@inertiajs/vue3';
import { ref } from 'vue'; 


const primaryData = ref<string>('111;222;222;333;444;555;666;777;888;999;');
const secondaryData = ref<string>('000;444;444;888;121;232;343;888');

const primarySeparator = ref<string>(';');
const secondarySeparator = ref<string>(';');

const primaryList = ref<string[]>([]);
const secondaryList = ref<string[]>([]);

const preparedData = ref<{ value: string; source: string; both: boolean }[]>([]);

const options = ref<{ trim: boolean; removeDuplicates: boolean; removeDuplicatesAfter: boolean; removeEmpty: boolean }>({
    trim: false,
    removeDuplicates: false,
    removeDuplicatesAfter: false,
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

    removeDuplicatesAfter();
    sortResults();
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

const removeDuplicatesAfter = () => {
    if (! options.value.removeDuplicatesAfter) {
        return;
    }

    preparedData.value = Array.from(new Map(preparedData.value.map(item => [item.value, item])).values());
};

const sortResults = () => {
    preparedData.value.sort((a, b) => a.value.localeCompare(b.value));
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
