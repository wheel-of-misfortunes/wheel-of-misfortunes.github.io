<template>
    <div v-if="!goToSlotMachine">
        <div class="thumbnail-container">
            <img class="thumbnail" src="../assets/content/thumbnail.png"/>
            <h1>Slot Machine of Pain</h1>
        </div>
      <div class="character-count-input-container">
        <label for="characterCountInput">Enter character count to spin for:</label>
        <input
          id="characterCountInput"
          v-model="characterCount"
          type="number"
          name="Character Count"
          min="10"
          max="30"
        />
      </div>
      <button class="button" @click="goToSlotMachineNow">Start</button>
    </div>
    <SlotMachine v-if="goToSlotMachine" :characterCount="characterCount" />
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import SlotMachine from '~/components/SlotMachine.vue';

const goToSlotMachine = ref(false);
const characterCount = ref(20);

function goToSlotMachineNow() {
    if (characterCount.value >= 10 && characterCount.value <= 30) {
        goToSlotMachine.value = true;
    }
}
</script>

<style scoped>
.character-count-input-container {
    display: flex;
    flex-direction: column;
    align-items: center; /* Center align the input container */
    justify-content: center;
}

#characterCountInput {
    width: 100%;
    margin-top: 5px;
    color: #333; /* Input text color */
}

.thumbnail-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
}

.thumbnail{
    width: 50%;
    height: 50%;
}
</style>
