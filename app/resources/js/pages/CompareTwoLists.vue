<template>
    <Head title="Compare Two Lists">
        <link rel="preconnect" href="https://rsms.me/" />
        <link rel="stylesheet" href="https://rsms.me/inter/inter.css" />
    </Head>

    <div>
        <section class="text-center my-6 space-y-3">
            <div class="flex-1 text-5xl">Compare Two Lists</div>
            <div class="text-md text-gray-400">Compare two dataset and find differences</div>
        </section>

        <section class="p-4">
            <div class="flex gap-4">
                <div class="flex-1 bg-white p-4 rounded-xl shadow-sm">
                    <p class="text-xl mb-2">Primary List</p>

                    <div class="flex gap-2 items-center mb-4">
                        <div>Separator:</div>
                        <input v-model="primarySeparator" type="text" name="primary-separator" class="w-full p-2 border-1 rounded-sm" placeholder="Primary separator" />
                    </div>

                    <textarea v-model="primaryData" class="w-full p-2 border-1 rounded-sm" placeholder="Paste your primary text" rows="15"></textarea>
                    <div>{{ numberOfPrimaryData }} items detected</div>
                </div>
                <div class="flex-1 bg-white p-4 rounded-xl shadow-sm">
                    <p class="text-xl mb-2">Secondary List</p>

                    <div class="flex gap-2 items-center mb-4">
                        <div>Separator:</div>
                        <input v-model="secondarySeparator" type="text" name="secondary-separator" class="w-full p-2 border-1 rounded-sm" placeholder="Secondary separator" />
                    </div>

                    <textarea v-model="secondaryData" class="w-full p-2 border-1 rounded-sm" placeholder="Paste your secondary text" rows="15"></textarea>
                    <div>{{ numberOfSecondaryData }} items detected</div>
                </div>
            </div>

            <div class="my-8 bg-white p-4 rounded-xl shadow-sm">
                <p class="text-xl mb-2">
                    <svg class="inline-block w-6 h-6 relative top-[-1px]" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="none" viewBox="0 0 24 24">
                        <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 13v-2a1 1 0 0 0-1-1h-.757l-.707-1.707.535-.536a1 1 0 0 0 0-1.414l-1.414-1.414a1 1 0 0 0-1.414 0l-.536.535L14 4.757V4a1 1 0 0 0-1-1h-2a1 1 0 0 0-1 1v.757l-1.707.707-.536-.535a1 1 0 0 0-1.414 0L4.929 6.343a1 1 0 0 0 0 1.414l.536.536L4.757 10H4a1 1 0 0 0-1 1v2a1 1 0 0 0 1 1h.757l.707 1.707-.535.536a1 1 0 0 0 0 1.414l1.414 1.414a1 1 0 0 0 1.414 0l.536-.535 1.707.707V20a1 1 0 0 0 1 1h2a1 1 0 0 0 1-1v-.757l1.707-.708.536.536a1 1 0 0 0 1.414 0l1.414-1.414a1 1 0 0 0 0-1.414l-.535-.536.707-1.707H20a1 1 0 0 0 1-1Z"/>
                        <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15a3 3 0 1 0 0-6 3 3 0 0 0 0 6Z"/>
                    </svg>

                    Options
                </p>

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
                <span class="px-6 py-3 bg-green-600 text-white font-bold cursor-pointer rounded-sm hover:bg-green-500 active:bg-green-600 select-none" @click="prepareLists">
                    <svg class="inline-block w-6 h-6 relative top-[-1px]" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="none" viewBox="0 0 24 24">
                        <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="m10.051 8.102-3.778.322-1.994 1.994a.94.94 0 0 0 .533 1.6l2.698.316m8.39 1.617-.322 3.78-1.994 1.994a.94.94 0 0 1-1.595-.533l-.4-2.652m8.166-11.174a1.366 1.366 0 0 0-1.12-1.12c-1.616-.279-4.906-.623-6.38.853-1.671 1.672-5.211 8.015-6.31 10.023a.932.932 0 0 0 .162 1.111l.828.835.833.832a.932.932 0 0 0 1.111.163c2.008-1.102 8.35-4.642 10.021-6.312 1.475-1.478 1.133-4.77.855-6.385Zm-2.961 3.722a1.88 1.88 0 1 1-3.76 0 1.88 1.88 0 0 1 3.76 0Z"/>
                    </svg>

                    Compare data
                </span>
            </div>
        </section>

        <section class="p-4">
            <div class="text-center bg-white p-4 rounded-xl shadow-sm">

                <div class="grid grid-cols-3 gap-4 ">
                    <div class="flex-1">
                        <p class="flex justify-center gap-2 mb-2">
                            <span class="text-xl">
                                <svg class="inline-block w-6 h-6 relative top-[-1px] text-amber-600" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="currentColor" viewBox="0 0 24 24">
                                    <path fill-rule="evenodd" d="M2 12C2 6.477 6.477 2 12 2s10 4.477 10 10-4.477 10-10 10S2 17.523 2 12Zm13.707-1.293a1 1 0 0 0-1.414-1.414L11 12.586l-1.793-1.793a1 1 0 0 0-1.414 1.414l2.5 2.5a1 1 0 0 0 1.414 0l4-4Z" clip-rule="evenodd"/>
                                </svg>

                                Only in Primary
                            </span>
                            <span class="text-sm bg-gray-200 text-gray-600 rounded-md p-1">{{ countPrimaryData() }} items</span>
                        </p>
                    </div>
                    <div class="flex-1">
                        <p class="flex justify-center gap-2 mb-2">
                            <span class="text-xl">
                                <svg class="inline-block w-6 h-6 relative top-[-1px] text-green-600" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="currentColor" viewBox="0 0 24 24">
                                    <path fill-rule="evenodd" d="M2 12C2 6.477 6.477 2 12 2s10 4.477 10 10-4.477 10-10 10S2 17.523 2 12Zm13.707-1.293a1 1 0 0 0-1.414-1.414L11 12.586l-1.793-1.793a1 1 0 0 0-1.414 1.414l2.5 2.5a1 1 0 0 0 1.414 0l4-4Z" clip-rule="evenodd"/>
                                </svg>

                                In Both Lists
                            </span>
                            <span class="text-sm bg-gray-200 text-gray-600 rounded-md p-1">{{ countBothData() }} items</span>
                        </p>
                    </div>
                    <div class="flex-1">
                        <p class="flex justify-center gap-2 mb-2">
                            <span class="text-xl">
                                <svg class="inline-block w-6 h-6 relative top-[-1px] text-sky-600" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="currentColor" viewBox="0 0 24 24">
                                    <path fill-rule="evenodd" d="M2 12C2 6.477 6.477 2 12 2s10 4.477 10 10-4.477 10-10 10S2 17.523 2 12Zm13.707-1.293a1 1 0 0 0-1.414-1.414L11 12.586l-1.793-1.793a1 1 0 0 0-1.414 1.414l2.5 2.5a1 1 0 0 0 1.414 0l4-4Z" clip-rule="evenodd"/>
                                </svg>

                                Only in Secondary
                            </span>
                            <span class="text-sm bg-gray-200 text-gray-600 rounded-md p-1">{{ countSecondaryData() }} items</span>
                        </p>
                    </div>
                </div>

                <div class="[&>*:nth-child(odd)]:bg-gray-50 [&>*:nth-child(even)]:bg-white">
                     <template v-for="data in preparedData">
                        <div class="grid grid-cols-3 gap-4 mb-1 hover:bg-gray-100!">
                            <div class="h-[24px]">
                                <span
                                    class="bg-gray-200 rounded-sm whitespace-pre"
                                    :class="data.source === 'P' && ! data.both ? 'px-2' : ''"
                                >{{ data.source === 'P' && ! data.both ? data.value : '' }}</span>
                            </div>
                            <div class="h-[24px]">
                                <span
                                    class="bg-gray-200 rounded-sm whitespace-pre"
                                    :class="data.both ? 'px-2' : ''"
                                >{{ data.both ? data.value : '' }}</span>
                            </div>
                            <div class="h-[24px]">
                                <span
                                    class="bg-gray-200 rounded-sm whitespace-pre"
                                    :class="data.source === 'S' && ! data.both ? 'px-2' : ''"
                                    >{{ data.source === 'S' && ! data.both ? data.value : '' }}</span>
                            </div>
                        </div>
                    </template>
                </div>
            </div>

            <div v-if="preparedData.length === 0" class="text-center p-8 text-gray-400">
                <svg class="w-6 h-6 inline relative top-[-2px]" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="none" viewBox="0 0 24 24">
                    <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 11h2v5m-2 0h4m-2.592-8.5h.01M21 12a9 9 0 1 1-18 0 9 9 0 0 1 18 0Z"/>
                </svg>
                Click "Compare data" to show results
            </div>
        </section>

        <section class="mt-8 p-4 bg-green-200 text-green-800 text-center border-t-2 border-t-green-800">
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
import { ref, watch } from 'vue'; 


const primaryData = ref<string>('111;222;222;333;444;555;666;777;888;999;');
const secondaryData = ref<string>('000;444;444;888;121;232;343;888');

const primarySeparator = ref<string>(';');
const secondarySeparator = ref<string>(';');

const primaryList = ref<string[]>([]);
const secondaryList = ref<string[]>([]);

const numberOfPrimaryData = ref<number>(0);
const numberOfSecondaryData = ref<number>(0);

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

watch([primaryData, primarySeparator], () => {
    countPrimaryRawData();
});

watch([secondaryData, secondarySeparator], () => {
    countSecondaryRawData();
});

const countPrimaryRawData = () => {
    const count = primaryData.value.split('').filter(s => s === primarySeparator.value).length;
    numberOfPrimaryData.value = count === 0 ? 0 : count + 1;
};
countPrimaryRawData();

const countSecondaryRawData = () => {
    const count = secondaryData.value.split('').filter(s => s === secondarySeparator.value).length;
    numberOfSecondaryData.value = count === 0 ? 0 : count + 1;
}
countSecondaryRawData();

const countPrimaryData = () => {
    return preparedData.value.filter(v => v.source === 'P' && ! v.both).length;
};

const countBothData = () => {
    return preparedData.value.filter(v => v.both).length;
};

const countSecondaryData = () => {
    return preparedData.value.filter(v => v.source === 'S' && ! v.both).length;
};
</script>
