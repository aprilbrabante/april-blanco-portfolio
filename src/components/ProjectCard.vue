<template>
    <div class="col-12 col-md-4">
        <div class="card h-100 rounded shadow">
            <img :src="project.image" class="card-img-top" alt="...">
            <div class="card-body">
            <h5 class="card-title">{{ project.title }}</h5>
            <p class="card-text">
                {{ project.description }}
            </p>
            </div>
        </div>
    </div>
</template>

<script setup>
    import { defineProps } from 'vue';

    defineProps({
        project: Object
    })
</script>