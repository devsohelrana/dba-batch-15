<script setup>

import { ref, onMounted } from 'vue';

const students = ref([]);
const studentImages = ref({});
const defaultImage = './img/profile.jpg';

onMounted(() => {
    fetch('/students.json')
        .then(response => response.json())
        .then(data => {
            students.value = data;
            loadStudentImages(); // Load images after fetching students
        })
        .catch(error => console.error('Error fetching student data:', error));
});

// Function to load student images
const loadStudentImages = () => {
    students.value.forEach((student) => {
        // Check for both .jpg and .png formats
        const formats = ['jpg', 'png'];
        let imageFound = false;

        formats.forEach(format => {
            const imagePath = `./img/student/${student.Roll}.${format}`;
            const img = new Image();

            // Set up image load and error handlers
            img.onload = () => {
                studentImages.value[student.Roll] = imagePath; // Image exists, save the path
                imageFound = true; // Set imageFound to true if an image is successfully loaded
            };
            img.onerror = () => {
                if (!imageFound && format === formats[formats.length - 1]) {
                    studentImages.value[student.Roll] = defaultImage; // Use default if none exist
                }
            };

            // Start loading the image
            img.src = imagePath;
        });
    });
};
</script>


<template>
    <main class="lg:overflow-y-scroll lg:h-screen">
        <div class="space-y-2 md:flex flex-wrap py-5">
            <div v-for="student in students" :key="student.Roll"
                class="p-4 max-w-md mx-auto space-y-2 bg-white rounded-xl shadow-lg sm:py-4 sm:flex sm:items-center sm:space-y-0 sm:gap-x-6">
                <div class="block mx-auto h-24 w-24 rounded-full sm:mx-0 sm:shrink-0 overflow-hidden border">
                    <img :src="studentImages[student.Roll] || defaultImage" alt="DBA" />
                </div>

                <div class="text-center space-y-2 sm:text-left">
                    <div class="space-y-0.5 mb-2">
                        <p class="text-lg text-black font-semibold">{{ student.Name }}</p>
                        <p class="text-slate-500 font-medium">{{ student.Roll }}</p>
                    </div>
                    <a :href="'https://www.facebook.com/' + student['Facebook URL']" target="_blank">
                        <button
                            class="px-4 py-1 text-sm text-purple-600 font-semibold rounded-full border border-purple-200 hover:text-white hover:bg-purple-600 hover:border-transparent focus:outline-none focus:ring-2 focus:ring-purple-600 focus:ring-offset-2">
                            Profile
                        </button>
                    </a>
                </div>
            </div>
        </div>
    </main>
</template>