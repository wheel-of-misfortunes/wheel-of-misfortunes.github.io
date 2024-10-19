<template>
    <main class="flex flex-row min-w-screen min-h-screen bg-dark-palette">
        <div id="body" class="w-full">
            <slot /> 
        </div>
    </main>
</template>

<style scoped>
.bg-dark-palette {
    background-color: #1c1c1c; /* Darker gray for a more neutral background */
    padding: 20px; /* Adding some padding */
    border-radius: 15px; /* Slightly larger rounding for a softer look */
    box-shadow: 0 8px 30px rgba(0, 0, 0, 0.5); /* Stronger shadow for depth */
}

#body {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    text-align: center;
}
</style>
