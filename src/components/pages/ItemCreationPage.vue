<template>
  <div class="item-creator">
    <div class="top-bar" @click="toggleOpen">
      <p class="centered-text">
        <span class="bordered-text">
          {{open ? `▼ ${selected_small_number}` : `▶ ${selected_small_number}`}}
        </span>
      </p>    </div>
    <div class="flex-container">
      <div class="content" v-show="open">
        <div class="align-center">
          <div class="item-detail">
            <img v-if="item.material" :src="getIcon(item.material)" alt="?" class="item-icon">
            <select v-model="item.material" class="item-select">
              <option v-for="name in itemIDs" :key="name" :value="name">
                {{ formatItemName(name) }}
              </option>
            </select>
          </div>
          <div class="input-field">
            <input type="number" v-model="item.selected_large_number" placeholder="Crate Number" class="item-input-number">
          </div>
          <div class="input-field">
            <input v-model="item.name" placeholder="Name" class="item-name">
          </div>
          <div v-for="(lore, index) in item.lore" :key="index" class="textarea-field inline-field">
            <textarea v-model="item.lore[index]" placeholder="Lore" class="item-lore"></textarea>
            <button v-if="index === item.lore.length - 1" class="add-button" @click="addLoreField">+</button>
          </div>
          <button class="generate-button" @click="fillTextBoxes">Generate</button>
        </div>
      </div>
      <div class="right-side" v-show="open">
        <div class="bordered-textarea align-center">
          <div class="textarea-field">
            <textarea v-model="textBox1" disabled placeholder="Item Collector Info" class="readonly-item-lore"></textarea>
          </div>
          <div class="textarea-field">
            <textarea v-model="textBox2" disabled placeholder="CrazyCrates Info" class="readonly-item-lore"></textarea>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import { ref } from 'vue';
import Items from '../../../public/items.json';
import app from "@/App.vue";

export default {
  props: {
    selected_small_number: {
      type: Number,
      required: true
    },
    passed_month: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      item: {
        material: null,
        name: "",
        lore: [""],
        selected_month: this.passed_month,
        item_number: this.selected_small_number,
        selected_large_number: null
      },
      Items,
      textBox1: "",
      textBox2: "",
      current_year: new Date().getFullYear(),
      open: false
    };
  },
  computed: {
    itemIDs() {
      return this.Items.map(item => item.name).sort();
    },
  },

  watch: {
    selected_month: {
      handler(newMonth) {
        this.item.selected_month = newMonth;
      },
    },
  },

  methods: {
    formatItemName(name) {
      return name.split('_')
          .map(word => word.charAt(0).toUpperCase() + word.slice(1))
          .join(' ');
    },
    getIcon(name) {
      return `./items/${name}.png`;
    },
    addLoreField() {
      this.item.lore.push("");
    },
    fillTextBoxes(){
      const lore = this.item.lore.join('. ');
      this.textBox1 = `
  '${this.selected_small_number}'\n\
    material: ${this.item.material.toUpperCase()}\n\
    name: '${this.item.name}'\n\
    lore:\n\
    - '&8${lore.split('. ').join("'\n    - '&8")}'\n\
    - '&8      '\n\
    - '&e${this.passed_month} ${this.current_year} Collectors Item'`;

      this.textBox2 = `
'${this.item.selected_large_number}':
    DisplayName: '&8${this.item.name}'
    DisplayItem: ${this.item.material.toUpperCase()}
    DisplayAmount: 1
    Lore:
    - '&8${lore.split('. ').join("\n    - '&8")}'
    - '&8      '
    - '&e${this.passed_month} ${this.current_year} Collectors Item'
    - '&f3%'
    MaxRange: 100
    Chance: 3
    Firework: false
    Glowing: true
    Player: ''
    unbreakable: true
    HideItemFlags: true
    Items:
    - Item:${this.item.material.toUpperCase()}, Amount:1, Name:&8${this.item.name}, UNBREAKING:1, HIDE_ENCHANTS, Lore:&8${lore.split('. ').join(",&8      ")},&8      ,&e${this.passed_month} ${this.current_year} Collectors Item
    `;
    },
    toggleOpen() {
      this.open = !this.open;
    }
  },
};
</script>
<style scoped>
.item-creator {
  display: flex;
  flex-direction: column;
  gap: 20px;
  width: 100%;
  padding-bottom: 20px; /* Add extra spacing at the bottom */
}

.top-bar {
  display: flex;
  flex-direction: column;
  justify-content: center;
  height: 100%;
}

.flex-container {
  display: flex;
  justify-content: flex-end;
}

.inline-field {
  display: flex;
  align-items: start;
}

.bordered-text {
  border: 1px solid #000;
  padding: 5px;
}

/* New class to give a border to div containing textareas */
.bordered-textarea {
  border: 2px solid #000;
  padding: 15px;
  margin-bottom: 15px;
}

.add-button {
  margin-left: 10px;
}

.item-input-number {
  margin: 20px 0;
  padding: 10px;
  background-color: #ccc;
  cursor: pointer;
}

.content {
  font-size: 18px;
  border: 1px solid #ccc;
  border-radius: 3px;
}

.readonly-item-lore {
  font-size: 16px;
  padding: 10px;
  margin-top: 20px;
  width: 100%;
  max-width: 500px;
  border: 1px solid #000;
  border-radius: 4px;
  height: 100px;
  background: #eee;
}
</style>
