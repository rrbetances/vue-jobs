<script setup>
    import { ref, onMounted, reactive } from 'vue'
    import JobListing from './JobListing.vue';
    import { defineProps } from 'vue';
    import { RouterLink } from 'vue-router'
    import axios from 'axios'
    import PulseLoader from 'vue-spinner/src/PulseLoader.vue'

    const state = reactive({
        jobs: [],
        isLoading: true
    });

    const limit = ref(3);

    const showButton = ref(true);

    const showAll = () => {
        limit.value = null;
        showButton.value = false;
    }

    onMounted(async () => {
        try {  
            const apiUrl = import.meta.env.VITE_BASE_URL;
            const response = await axios.get(`${apiUrl}/jobs`);
            state.jobs = response.data;

        } catch (error) {
            console.error("Error fetching jobs", error)
        }
        finally{
            state.isLoading = false;
        }
    });

</script>

<template>
    <section class="bg-blue-50 px-4 py-10">
        <div class="container-xl lg:container m-auto">
            <h2 class="text-3xl font-bold text-green-500 mb-6 text-center">
                Browse Jobs
            </h2>
            <div v-if="state.isLoading" class="text-center text-gray-500 py-6">
                <PulseLoader />
            </div>

            <div v-else class="grid gid-cols-1 md:grid-cols-3 gap-6">
                <JobListing v-for="job in state.jobs.slice(0, limit || state.jobs.lenght)" :key=job.id :job=job />
            </div>
        </div>
    </section>

    <section v-if="showButton" class="m-auto max-w-lg my-10 px-6">
      <a
        @click="showAll"
        class="block bg-black text-white text-center py-4 px-6 rounded-xl hover:bg-gray-700"
        >View All Jobs</a
      >
    </section>
</template>