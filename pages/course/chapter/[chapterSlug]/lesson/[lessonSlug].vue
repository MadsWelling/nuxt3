<template>
  <div>
    <p class="mt-0 uppercase font-bold text-slate-400 mb-1">
      Lesson {{ chapter.number }}
    </p>
    <h2 class="my-0 text-bold">{{ lesson.title }}</h2>

    <div class="flex space-x-4 mt-2 mb-8">
      <NuxtLink
        class="text-slate-400"
        v-if="lesson.sourceUrl"
        :to="lesson.sourceUrl"
        >Download Source Code
      </NuxtLink>

      <NuxtLink
        class="text-grey-500 font-normal text-md"
        v-if="lesson.downloadUrl"
        :to="lesson.downloadUrl"
        >Download Video
      </NuxtLink>
    </div>
    <VideoPlayer
      v-if="lesson.videoId"
      :videoId="lesson.videoId"
      class="w-full"
    />

    <p>{{ lesson.text }}</p>
    <ClientOnly>
      <LessonComplete
        :model-value="isLessonComplete"
        @update:model-value="toggleComplete()"
      />
    </ClientOnly>
  </div>
</template>

<script setup>
const course = useCourse();
const route = useRoute();

definePageMeta({
  middleware: [function ({ params }, from) {
    const course = useCourse();

    const chapter = course.chapters.find(
      (chapter) => chapter.slug === params.chapterSlug
    );

    if (!chapter) {
      return abortNavigation(
        createError({
          statusCode: 404,
          message: "Chapter not found",
        })
      );
    }

    const lesson = chapter.lessons.find(
      (lesson) => lesson.slug === params.lessonSlug
    );

    if (!lesson) {
      return abortNavigation(
        createError({
          statusCode: 404,
          message: "Lesson not found",
        })
      );
    }
  },
  'auth',

],
});

console.log(course);

if (route.params.lessonSlug === "3-typing-component-events") {
  console.log(route.paramsthatdoesnotexitswhoops.capitalizeIsNotAMethod());
}

const chapter = computed(() => {
  return course.chapters.find(
    (chapter) => chapter.slug === route.params.chapterSlug
  );
});

const lesson = computed(() => {
  return chapter.value.lessons.find(
    (lesson) => lesson.slug === route.params.lessonSlug
  );
});

const title = computed(() => {
  return `${lesson.value.title} - ${course.title}`;
});

useHead({
  title: title,
});

const progress = useLocalStorage("Chapters - Progress", []);

const isLessonComplete = computed(() => {
  if (!progress.value[chapter.value.number - 1]) {
    return false;
  }

  if (!progress.value[chapter.value.number - 1][lesson.value.number - 1]) {
    return false;
  }

  return progress.value[chapter.value.number - 1][lesson.value.number - 1];
});

const toggleComplete = () => {
  if (!progress.value[chapter.value.number - 1]) {
    progress.value[chapter.value.number - 1] = [];
  }

  progress.value[chapter.value.number - 1][lesson.value.number - 1] =
    !isLessonComplete.value;
};
</script>
