<template>
 <!-- My Projects -->
    <section class="projects py-5" id="projects">
        <div class="container">
            <h2 class="fw-bold text-center section-color">MY PROJECTS</h2>
            <div class="row g-4">
            <ProjectCard
              v-for="(project, index) in projects"
              :key="index"
              :project="project"
            />
            </div>
        </div>
    </section>

</template>

<script setup>
    import { computed } from 'vue';
    import ProjectCard from './ProjectCard.vue';
    import projects from '../data/projects.json';

</script>