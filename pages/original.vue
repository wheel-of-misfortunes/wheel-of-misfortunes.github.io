<template>
    <div id="app">
      <div class="container">
        <h1>Raid Composition Role and Class Assignment</h1>
  
        <!-- Input field for entering one character at a time -->
        <input 
          v-model="newCharacter" 
          @keyup.enter="addCharacter" 
          placeholder="Enter character name and press Enter" 
        />
        <button @click="addCharacter">Add Character</button>
  
        <!-- Display list of added characters -->
        <ul v-if="characters.length">
          <li v-for="(character, index) in characters" :key="index">
            {{ character }} 
            <button @click="removeCharacter(index)">Remove</button>
          </li>
        </ul>
  
        <!-- Button to assign roles and classes after adding characters -->
        <button 
          :disabled="characters.length < 10 || characters.length > 30"
          @click="assignRolesAndClassesToRaid"
        >
          Spin!
        </button>
  
        <div v-if="error">{{ error }}</div>
  
        <!-- Display result -->
        <div v-if="assignedRoles.length">
          <h2>Assigned Roles and Classes:</h2>
          <ul>
            <li v-for="(role, index) in assignedRoles" :key="index">
              {{ role.name }} - {{ role.role }} - {{ role.class }} ({{ role.damageType }})
            </li>
          </ul>
        </div>
      </div>
    </div>
  </template>
  
<script lang="ts">
  import { ref } from 'vue';
  
  interface RoleAssignment {
    name: string;
    role: string;
    class?: string;
    damageType?: string; // Melee or Ranged for Damage Dealers
  }
  
  interface RoleDistribution {
    tanks: number;
    healers: number;
    damageDealers: number;
  }
  
  interface ClassOptions {
    [role: string]: string[];
  }
  
  // Role-specific class mappings with damage types for Damage Dealers
  const classOptions: ClassOptions = {
    Tank: ['Warrior', 'Paladin', 'Monk', 'Druid', 'Demon Hunter', 'Death Knight'],
    Healer: ['Paladin', 'Priest', 'Shaman', 'Monk', 'Druid', 'Evoker'],
    DamageDealerMelee: ['Warrior', 'Paladin', 'Rogue', 'Monk', 'Druid', 'Demon Hunter', 'Death Knight', 'Shaman'],
    DamageDealerRanged: ['Hunter', 'Priest', 'Shaman', 'Mage', 'Warlock', 'Druid', 'Evoker']
  };
  
  export default {
    setup() {
      const newCharacter = ref<string>(''); // New character input
      const characters = ref<string[]>([]); // List of added characters
      const assignedRoles = ref<RoleAssignment[]>([]); // Final role and class assignments
      const error = ref<string>(''); // Error messages
  
      let meleeCount = 0;
      let rangedCount = 0;
  
      // Add a character to the list and clear the input
      const addCharacter = () => {
        const trimmedName = newCharacter.value.trim();
        if (trimmedName.length > 0) {
          characters.value.push(trimmedName);
          newCharacter.value = '';
        }
      };
  
      // Remove a character from the list
      const removeCharacter = (index: number) => {
        characters.value.splice(index, 1);
      };
  
      // Validate the number of characters
      const validateCharacterCount = (count: number): boolean => {
        if (count < 10 || count > 30) {
          error.value = 'Raid composition must be between 10 and 30 characters.';
          assignedRoles.value = [];
          return false;
        }
        return true;
      };
  
      // Get the role distribution based on the number of characters
      const getRoleDistribution = (numCharacters: number): RoleDistribution => {
        let tanks = 2;
        let healers = 0;
  
        if (numCharacters <= 12) {
          healers = 2;
        } else if (numCharacters <= 15) {
          healers = 3;
        } else if (numCharacters <= 22) {
          healers = 4;
        } else if (numCharacters <= 26) {
          healers = 5;
        } else if (numCharacters <= 30) {
          healers = 6;
        }
  
        const damageDealers = numCharacters - (tanks + healers);
        return { tanks, healers, damageDealers };
      };
  
      // Shuffle the character list to randomize the assignment
      const shuffleArray = (array: string[]): string[] => {
        for (let i = array.length - 1; i > 0; i--) {
          const j = Math.floor(Math.random() * (i + 1));
          [array[i], array[j]] = [array[j], array[i]];
        }
        return array;
      };
  
      // Assign roles to characters based on distribution
      const assignRoles = (
        characters: string[],
        distribution: RoleDistribution
      ): RoleAssignment[] => {
        const roles: RoleAssignment[] = [];
  
        // Assign tanks
        for (let i = 0; i < distribution.tanks; i++) {
          roles.push({ name: characters[i], role: 'Tank' });
        }
  
        // Assign healers
        for (let i = distribution.tanks; i < distribution.tanks + distribution.healers; i++) {
          roles.push({ name: characters[i], role: 'Healer' });
        }
  
        // Assign damage dealers
        for (let i = distribution.tanks + distribution.healers; i < characters.length; i++) {
          roles.push({ name: characters[i], role: 'Damage Dealer' });
        }
  
        return roles;
      };
  
      // Assign damage dealer class, balancing Melee vs Ranged
      const assignDamageDealerClass = (roleAssignment: RoleAssignment): RoleAssignment => {
        const totalDamageDealers = meleeCount + rangedCount;
        const meleeRatio = meleeCount / totalDamageDealers;
        const rangedRatio = rangedCount / totalDamageDealers;
  
        let damageType: string;
        let availableClasses: string[];
  
        if (meleeRatio > 0.6) {
          // Too many melee, force ranged
          damageType = 'Ranged';
          availableClasses = classOptions.DamageDealerRanged;
        } else if (rangedRatio > 0.6) {
          // Too many ranged, force melee
          damageType = 'Melee';
          availableClasses = classOptions.DamageDealerMelee;
        } else {
          // Randomly assign either melee or ranged
          damageType = Math.random() < 0.5 ? 'Melee' : 'Ranged';
          availableClasses = classOptions[`DamageDealer${damageType}`];
        }
  
        // Choose a random class from the available ones for the damage type
        const randomClass = availableClasses[Math.floor(Math.random() * availableClasses.length)];
  
        // Update melee or ranged count based on assigned type
        if (damageType === 'Melee') {
          meleeCount++;
        } else {
          rangedCount++;
        }
  
        return {
          ...roleAssignment,
          class: randomClass,
          damageType
        };
      };
  
      // Randomly assign a class and damage type based on the character's role
      const assignClasses = (roles: RoleAssignment[]): RoleAssignment[] => {
        return roles.map(roleAssignment => {
          if (roleAssignment.role === 'Damage Dealer') {
            return assignDamageDealerClass(roleAssignment);
          } else {
            const availableClasses = classOptions[roleAssignment.role];
            const randomClass = availableClasses[Math.floor(Math.random() * availableClasses.length)];
            return {
              ...roleAssignment,
              class: randomClass
            };
          }
        });
      };
  
      // Main function to trigger role and class assignment
      const assignRolesAndClassesToRaid = () => {
        if (!validateCharacterCount(characters.value.length)) {
          return;
        }
  
        meleeCount = 0;
        rangedCount = 0;
  
        const roleDistribution = getRoleDistribution(characters.value.length);
        const shuffledCharacters = shuffleArray([...characters.value]);
  
        const roles = assignRoles(shuffledCharacters, roleDistribution);
        assignedRoles.value = assignClasses(roles);
        error.value = ''; // Clear error if any
      };
  
      return {
        newCharacter,
        characters,
        assignedRoles,
        error,
        addCharacter,
        removeCharacter,
        assignRolesAndClassesToRaid
      };
    }
  };
  </script>
  
  <style scoped>
/* General page styling for centering content */
body, html {
  height: 100%;
  margin: 0;
  font-family: 'Arial', sans-serif; /* Use a clean and readable font */
  background-color: #f7f7f7; /* Light background for contrast */
}

#app {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  text-align: center;
}

/* Container for the main content */
.container {
  background-color: rgb(30, 31, 34);
  padding: 40px;
  border-radius: 10px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
  width: 80%;
  max-width: 600px;
  margin: 20px;
}

h1 {
  font-size: 2rem;
  margin-bottom: 20px;
  color: rgb(226, 232, 240) !important;
}

/* Input styling for character name */
input {
  padding: 10px;
  width: 80%;
  color: rgb(30, 31, 34);
  font-size: 1rem;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin-bottom: 20px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

/* Button styling */
button {
  padding: 10px 20px;
  font-size: 1rem;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
  margin: 5px;
}

button:hover {
  background-color: #0056b3;
}

button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

/* Styling for the character list */
ul {
  list-style-type: none;
  padding: 0;
}

li {
  background-color: #f1f1f1;
  padding: 5px;
  margin: 5px 0;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  display: flex;
  justify-content: space-between;
  align-items: center;
}

/* Remove button for list items */
li button {
  background-color: #dc3545;
  padding: 5px 10px;
  border: none;
  border-radius: 5px;
  color: white;
  font-size: 0.9rem;
}

li button:hover {
  background-color: #c82333;
}

/* Role and class assignment section styling */
h2 {
  font-size: 1.5rem;
  margin-top: 30px;
  color: #333;
}

ul li {
  font-size: 1.1rem;
  color: #555;
}
</style>
