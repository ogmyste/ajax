<script setup>
import axios from "axios";
import { computed, reactive, ref } from "vue";
import Button from "../components/Button.vue";
import CharacterCard from "../components/CharacterCard.vue";

const statusOptions = [
    { label: "Alive", value: "alive" },
    { label: "Dead", value: "dead" },
    { label: "Unknown", value: "unknown" },
];

const genderOptions = [
    { label: "Female", value: "female" },
    { label: "Male", value: "male" },
    { label: "Genderless", value: "genderless" },
    { label: "Unknown", value: "unknown" },
];

const filterGroups = [
    { key: "status", label: "Status", options: statusOptions },
    { key: "gender", label: "Gender", options: genderOptions },
];

const filters = reactive({
    status: "",
    gender: "",
});

let characters = ref([]);
let pagination = ref({
    count: 0,
    pages: 0,
    next: null,
    prev: null,
});

let current = ref(1);
let searchInput = ref("");
let loading = ref(false);
let error = ref("");

let hasFilters = computed(() => Boolean(filters.status || filters.gender || searchInput.value.trim()));

// prevents an older, slower response from overwriting a newer one
let requestId = 0;

async function getCharacters(page = 1) {
    const id = ++requestId;
    current.value = page;
    loading.value = true;
    error.value = "";

    try {
        const res = await axios.get("https://rickandmortyapi.com/api/character", {
            params: {
                page: page,
                name: searchInput.value.trim() || undefined,
                status: filters.status || undefined,
                gender: filters.gender || undefined,
            },
        });
        if (id !== requestId) return;
        characters.value = res.data.results;
        pagination.value = res.data.info;
    } catch (e) {
        if (id !== requestId) return;
        characters.value = [];
        pagination.value = { count: 0, pages: 0, next: null, prev: null };
        error.value = e.response?.status === 404
            ? "No characters match these filters."
            : "Could not load characters. Please try again.";
    } finally {
        if (id === requestId) loading.value = false;
    }
}

await getCharacters(1);

async function next() {
    await getCharacters(current.value + 1);
}

async function prev() {
    await getCharacters(current.value - 1);
}

function setFilter(key, value) {
    if (loading.value) return;
    // clicking the active filter clears it
    filters[key] = filters[key] === value ? "" : value;
    getCharacters(1);
}

function clearAll() {
    if (loading.value) return;
    clearTimeout(searchTimeout);
    filters.status = "";
    filters.gender = "";
    searchInput.value = "";
    getCharacters(1);
}

let pages = computed(() => {
    const total = pagination.value.pages;
    const active = current.value;
    if (!total) return [];

    const from = Math.max(1, active - 2);
    const to = Math.min(total, active + 2);

    let list = [];
    for (let i = from; i <= to; i++) {
        list.push(i);
    }
    if (from > 1) {
        list = [1, ...(from > 2 ? ["..."] : []), ...list];
    }
    if (to < total) {
        list = [...list, ...(to < total - 1 ? ["..."] : []), total];
    }

    return list;
});

//debounce
let searchTimeout = null;

function search() {
    clearTimeout(searchTimeout);
    searchTimeout = setTimeout(() => {
        getCharacters(1);
    }, 1000);
}

function searchNow() {
    if (loading.value) return;
    clearTimeout(searchTimeout);
    getCharacters(1);
}
</script>
<template>
    <div class="container">

        <div class="field has-addons">
            <div class="control is-expanded">
                <input @input="search" v-model="searchInput" class="input" type="text" placeholder="Find a character"
                    :disabled="loading">
            </div>
            <div class="control">
                <Button color="info" :loading="loading" :disabled="loading" @click="searchNow">
                    Search
                </Button>
            </div>
        </div>

        <nav class="level filter-bar">
            <div class="level-left">
                <div class="level-item">
                    <div class="field is-grouped is-grouped-multiline">
                        <div class="control" v-for="group in filterGroups" :key="group.key">
                            <div class="is-flex is-align-items-center is-flex-wrap-wrap">
                                <p class="subtitle is-6 mb-0 mr-3"><strong>{{ group.label }}:</strong></p>
                                <div class="buttons has-addons mb-0">
                                    <Button v-for="option in group.options" :key="option.value" size="small" :color="filters[group.key] === option.value
                                        ? 'link'
                                        : undefined" :light="filters[group.key] !== option.value" :disabled="loading"
                                        @click="setFilter(group.key, option.value)">
                                        {{ option.label }}
                                    </Button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="level-right">
                <div class="level-item">
                    <Button color="danger" outlined :disabled="!hasFilters || loading" @click="clearAll">
                        Clear All
                    </Button>
                </div>
            </div>
        </nav>

        <div class="is-flex is-align-items-center is-justify-content-space-between is-flex-wrap-wrap mb-4">
            <p class="subtitle is-6 mb-0 result-count">
                <strong>{{ pagination.count }}</strong> character{{ pagination.count === 1 ? '' : 's' }} found
                <span v-if="filters.status || filters.gender" class="tag is-link is-light ml-2">filtered</span>
            </p>
            <span v-if="loading" class="tag is-light">Loading&hellip;</span>
        </div>

        <article v-if="error" class="message is-danger">
            <div class="message-body">
                <div class="is-flex is-align-items-center is-justify-content-space-between is-flex-wrap-wrap">
                    <span>{{ error }}</span>
                    <Button v-if="hasFilters" color="danger" outlined size="small" :disabled="loading"
                        @click="clearAll">Clear All</Button>
                </div>
            </div>
        </article>

        <nav class="pagination is-centered" role="navigation" aria-label="pagination">
            <button class="pagination-previous" :disabled="!pagination.prev || loading" @click="prev">
                Previous
            </button>
            <button class="pagination-next" :disabled="!pagination.next || loading" @click="next">
                Next page
            </button>
            <ul class="pagination-list">
                <li v-for="(page, index) in pages" :key="index + '-' + page">
                    <span v-if="page === '...'" class="pagination-ellipsis">&hellip;</span>
                    <button v-else-if="page === current" class="pagination-link is-current" :aria-label="'Page ' + page"
                        aria-current="page" :disabled="loading">
                        {{ page }}
                    </button>
                    <button v-else class="pagination-link" :aria-label="'Goto page ' + page" :disabled="loading"
                        @click="getCharacters(page)">
                        {{ page }}
                    </button>
                </li>
            </ul>
        </nav>

        <div class="columns is-multiline">
            <div class="column is-3" v-for="character in characters" :key="character.id">
                <CharacterCard :character="character"></CharacterCard>
            </div>
        </div>
    </div>
</template>
<style scoped>
.filter-bar {
    flex-wrap: wrap;
    row-gap: 0.75rem;
}

@media screen and (max-width: 768px) {
    .filter-bar {
        display: block;
    }

    .filter-bar .level-left,
    .filter-bar .level-right {
        flex-wrap: wrap;
    }

    .filter-bar .level-item {
        justify-content: flex-start;
    }

    .filter-bar .level-right {
        margin-top: 1rem;
    }
}
</style>
