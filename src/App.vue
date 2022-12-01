<template>
  <div style="width: 50%; margin: 0 auto">
    <Input v-model="kamal" type="password" />

    <br />

    <Select />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

// Components
// import Button from "@/components/Button/index.vue";
// import Link from "@/components/Link/index.vue";
// import RadioList from "@/components/RadioList/index.vue";
// import Checkbox from "@/components/Checkbox/index.vue";
// import CheckList from "@/components/Checkbox/list.vue";
import Input from "@/components/Input/index.vue";
import Select from "@/components/Select/index.vue";

interface MainPage {
  kamal: any;
  isAllFruits: boolean;
  fruits: Array<string>;
}

export default defineComponent({
  name: "App",
  components: {
    Input,
    Select,
  },

  data(): MainPage {
    const data: MainPage = {
      kamal: null,
      isAllFruits: false,
      fruits: [],
    };

    return data;
  },

  methods: {
    someListener() {
      alert(1);
    },

    fruitsSelectToggle(isChecked: boolean, fruit: string): void {
      if (isChecked) {
        this.fruits.push(fruit);
        return;
      }

      this.fruits.splice(this.fruits.indexOf(fruit), 1);
    },
  },
});
</script>

<style src="@/assets/main.css"></style>
