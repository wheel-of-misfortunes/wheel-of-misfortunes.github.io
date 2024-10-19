<template>
    <main class="flex flex-row min-w-screen min-h-screen bg-dark-palette">
        <div id="body" class="w-full">
          <slot /> 
        </div>
    </main>
  </template>
  
  <style scoped>
  .bg-dark-palette {
    background-color: #002d66;
  }
  
    #body {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        text-align: center;

     @media (max-width: 768px) {
      }
    }
  </style>