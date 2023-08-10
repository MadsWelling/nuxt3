<template>
    <NuxtLayout>
    <div class="prose">
        <h4>404</h4>
        <p>I guess that page doesnt exist</p>
    </div>

    <h3> {{error.message}} </h3>

    </NuxtLayout>
</template>

<script setup>
const error = useError();

/* const handleError = () => {
    clearError({
        redirect: 
        '/course/chapter'
    })
}
 */

</script>
