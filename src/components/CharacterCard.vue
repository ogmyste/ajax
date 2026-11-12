<script setup>
import { computed } from 'vue';

let props = defineProps({
    character: {
        type: Object,
        required: true
    }
});

let statusColor = computed(() => {
    switch (props.character.status) {
        case 'Alive':
            return 'is-success';
        case 'Dead':
            return 'is-danger';
        default:
            return 'is-warning';
    }
});
</script>

<template>
    <div class="card">
        <header class="card-header">
            <p class="card-header-title">{{ character.name }}</p>
        </header>
        <div class="card-image">
            <figure class="image is-4by3">
                <img :src="character.image" :alt="character.name" />
            </figure>
        </div>
        <div class="card-content">
            <div class="tags mb-4">
                <span class="tag" :class="statusColor">{{ character.status }}</span>
                <span class="tag is-link is-light">{{ character.gender }}</span>
            </div>
            <div class="content">
                <table>
                    <tbody>
                        <tr>
                            <th>Status</th>
                            <td>{{ character.status }}</td>
                        </tr>
                        <tr>
                            <th>Gender</th>
                            <td>{{ character.gender }}</td>
                        </tr>
                        <tr>
                            <th>Type</th>
                            <td>{{ character.type ? character.type : 'No type' }}</td>
                        </tr>
                        <tr>
                            <th>Species</th>
                            <td>{{ character.species }}</td>
                        </tr>
                        <tr>
                            <th>Location</th>
                            <td>{{ character.location.name }}</td>
                        </tr>
                        <tr>
                            <th>Origin</th>
                            <td>{{ character.origin.name }}</td>
                        </tr>
                        <tr>
                            <th>Episodes</th>
                            <td>{{ character.episode.length }}</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</template>
