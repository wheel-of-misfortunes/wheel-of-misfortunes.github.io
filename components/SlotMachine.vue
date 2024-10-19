<template>
    <div class="page-container">
      <div class="slot-machine-logo-container">
        <img class="slot-machine-logo" src="../assets/content/7670528.png"/>
        <h1>Spinning for {{ characterCount }} players</h1>
      </div>
      <input type="text" v-model="nameInput" placeholder="Enter a character name for this spin" id="nameInput" />
      <div v-if="showRoleOptions">
        <div id="resultRole">Role: {{ resultRole }}</div>
      <div id="slotMachine">
        <div
        class="option"
        id="option1"
        :style="{ backgroundColor: slotColors[0] }">
            <span class="option-text">{{ slotOptions[0] }}</span>
        </div>
        <div
        class="option"
        id="option2"
        :style="{ backgroundColor: slotColors[1] }">
            <span class="option-text">{{ slotOptions[1] }}</span>
        </div>
        <div
        class="option"
        id="option3"
        :style="{ backgroundColor: slotColors[2] }">
            <span class="option-text">{{ slotOptions[2] }}</span>
        </div>
      </div>
      </div>
      <button id="spinButton" class="button" @click="spinOptions">Spin</button>
      <ul id="resultsList">
        <li v-for="result in results" :key="result">{{ result }}</li>
      </ul>
    </div>
  </template>
  
<script lang="ts" setup>
  import { ref } from 'vue';
  import { defineProps } from 'vue';

  const props = defineProps<{
    characterCount: number;
  }>();

  const slotColors = ref(['', '', '']);
  const showRoleOptions = ref(false);

  // Slot options with class names, roles, and colors
  const options = ref([
    { name: "Blood", role: "Tank", color: "#C41E3A" },
    { name: "Vengeance", role: "Tank", color: "#A330C9" },
    { name: "Guardian", role: "Tank", color: "#FF7C0A" },
    { name: "Protection", role: "Tank", color: "#C69B6D" },
    { name: "Protection", role: "Tank", color: "#F48CBA" },
    { name: "Brewmaster", role: "Tank", color: "#00FF98" },
    { name: "Holy", role: "Healer", color: "#FFFFFF" },
    { name: "Holy", role: "Healer", color: "#F48CBA" },
    { name: "Restoration", role: "Healer", color: "#FF7C0A" },
    { name: "Discipline", role: "Healer", color: "#FFFFFF" },
    { name: "Mistweaver", role: "Healer", color: "#00FF98" },
    { name: "Preservation", role: "Healer", color: "#33937F" },
    { name: "Retribution", role: "DPS", color: "#F48CBA" },
    { name: "Frost", role: "DPS", color: "#C41E3A" },
    { name: "Unholy", role: "DPS", color: "#C41E3A" },
    { name: "Frost", role: "DPS", color: "#3FC7EB" },
    { name: "Fire", role: "DPS", color: "#3FC7EB" },
    { name: "Arcane", role: "DPS", color: "#3FC7EB" },
    { name: "Havoc", role: "DPS", color: "#A330C9" },
    { name: "Balance", role: "DPS", color: "#FF7C0A" },
    { name: "Feral", role: "DPS", color: "#FF7C0A" },
    { name: "Survival", role: "DPS", color: "#AAD372" },
    { name: "Marksmanship", role: "DPS", color: "#AAD372" },
    { name: "Beast Mastery", role: "DPS", color: "#AAD372" },
    { name: "Elemental", role: "DPS", color: "#0070DD" },
    { name: "Enhancement", role: "DPS", color: "#0070DD" },
    { name: "Destruction", role: "DPS", color: "#8788EE" },
    { name: "Affliction", role: "DPS", color: "#8788EE" },
    { name: "Demonology", role: "DPS", color: "#8788EE" },
    { name: "Devastation", role: "DPS", color: "#33937F" },
    { name: "Augmentation", role: "DPS", color: "#33937F" },
    { name: "Arms", role: "DPS", color: "#C69B6D" },
    { name: "Fury", role: "DPS", color: "#C69B6D" },
    { name: "Assassination", role: "DPS", color: "#FFD433" },
    { name: "Outlaw", role: "DPS", color: "#FFF468" },
    { name: "Subtlety", role: "DPS", color: "#FFF468" },
    { name: "Windwalker", role: "DPS", color: "#00FF98" },
    { name: "Shadow", role: "DPS", color: "#FFFFFF" },
  ]);
  
  // Role distribution for spins
  const roleDistribution = ref([
    { role: "DPS", count: 9 },
    { role: "Healer", count: 3 },
    { role: "Tank", count: 2 }
  ]);
  
  let spinsLeft = ref(14);
  const spinOrder = ref<string[]>([]);
  
  const nameInput = ref('');
  const slotOptions = ref(['', '', '']);
  const resultRole = ref('');
  const results = ref<string[]>([]);
  
  // Create a random spin order
  function createSpinOrder() {
    for (const { role, count } of roleDistribution.value) {
      for (let i = 0; i < count; i++) {
        spinOrder.value.push(role);
      }
    }
    for (let i = spinOrder.value.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [spinOrder.value[i], spinOrder.value[j]] = [spinOrder.value[j], spinOrder.value[i]];
    }
  }
  
function spinOptions() {
  showRoleOptions.value = true;

  if (spinsLeft.value <= 0) {
    alert("All spins completed!");
    return;
  }

  // Add spin class to the slots for the animation effect
  const slots = document.querySelectorAll('.option');
  slots.forEach(slot => {
    slot.classList.add('spinning');
  });

  const spinInterval = setInterval(() => {
    const randomColors = [];
    const randomOptions = [];

    // Randomly pick colors and options from the available options array
    for (let i = 0; i < 3; i++) {
      const randomOption = options.value[Math.floor(Math.random() * options.value.length)];
      randomColors.push(randomOption.color);
      randomOptions.push(randomOption.name);
    }

    slotColors.value = randomColors;
    slotOptions.value = randomOptions;
  }, 100); // Change every 100ms to simulate the slot "spinning"

  setTimeout(() => {
    clearInterval(spinInterval); // Stop the rapid random changes

    const selectedRole = spinOrder.value[14 - spinsLeft.value];
    const filteredOptions = options.value.filter(option => option.role === selectedRole);
    const shuffledOptions = filteredOptions.sort(() => 0.5 - Math.random()).slice(0, 3);

    // Update the slot options and result role
    slotOptions.value = shuffledOptions.map(opt => opt.name);
    slotColors.value = shuffledOptions.map(opt => opt.color);
    resultRole.value = selectedRole;

    // Remove the spinning class after the animation ends
    slots.forEach(slot => {
      slot.classList.remove('spinning');
    });

    // Add result to the list
    const resultText = slotOptions.value.join(", ");
    const spinName = nameInput.value.trim() || "Unnamed Spin";
    results.value.push(`Roll ${14 - spinsLeft.value}: ${spinName}: ${resultText} (Role: ${selectedRole})`);

    spinsLeft.value--;
  }, 2500);
}

  createSpinOrder();
</script>
  
<style scoped>
.slot-machine-logo-container {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
}

.slot-machine-logo {
    width: 25%;
}

.page-container {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 100%;
    text-align: center;
    background-color: #1c1c1c; /* Darker gray for a more neutral background */
    padding: 20px; /* Adding some padding */
    border-radius: 15px; /* Slightly larger rounding for a softer look */
    box-shadow: 0 8px 30px rgba(0, 0, 0, 0.5); /* Stronger shadow for depth */
}

#slotMachine {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 10px;
    width: 600px;
    height: 120px;
    border: 5px solid #ffcc00; /* Gold border */
    border-radius: 10px;
    box-shadow: 0 4px 15px rgba(0, 0, 0, 0.5);
    overflow: hidden;
    background-color: #ffcc00; /* Dark background for the machine */
}

.option {
    text-align: center;
    font-size: 20px;
    font-weight: bold;
    line-height: 120px;
    color: rgb(230, 230, 230);
    transition: background-color 0.3s;
    display: flex;
    width: 100%;
    height: 90%;
    align-items: center;
    justify-content: center;
    background-color: #333; /* Dark background for options */
    border: 2px solid #1c1c1c; /* Black border around each option */
    border-radius: 5px; /* Optional: add some rounding to the corners */
}

.option-text {
    max-width: 100%;
    overflow: hidden;
}

#resultRole {
    font-size: 1.5rem; /* Slightly larger font */
    color: #ffcc00; /* Gold color for better visibility */
    margin-top: 10px;
    text-align: center;
    text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.5); /* Adding shadow for depth */
}

#resultsList {
    margin-top: 20px;
    color: #fff;
    list-style-type: none;
    padding: 0;
    text-align: left; /* Align results to the left for better readability */
}

#nameInput {
    width: 80%;
    max-width: 400px;
    margin-top: 20px; /* Added margin for spacing */
    padding: 10px; /* Added padding for better usability */
    border: 2px solid #ffcc00; /* Gold border */
    border-radius: 5px;
    background-color: #fff; /* White background for input */
}

#nameInput:focus {
    outline: none;
    border-color: #e6b800; /* Darker gold on focus */
}

.spin {
    animation: spin 1s ease-in-out forwards;
}

.option.spinning {
    animation: spin 2.5s ease-out forwards;
}

/* Responsive styles */
@media (max-width: 768px) {
    #slotMachine {
        height: 100px;
        width: 100%;
    }

    .slot-machine-logo {
        width: 90%;
    }
    
    .option {
        font-size: 22px;
        line-height: 100px;
    }
    
    #spinButton {
        font-size: 1rem;
    }
    
    #resultRole {
        font-size: 1rem;
    }
}

@media (max-width: 480px) {
    h1 {
        font-size: 1.5rem;
    }

    #slotMachine {
        height: 90px;
    }

    .option {
        font-size: 18px;
        line-height: 90px;
    }

    #spinButton {
        padding: 8px 16px;
        font-size: 0.9rem;
    }

    #nameInput {
        font-size: 1rem;
    }
}
</style>