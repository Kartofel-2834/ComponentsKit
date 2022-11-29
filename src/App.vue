<template>
  <Button type="danger" icon="tabler:brand-amongus" left-icon faded rounded>
    Imposter
  </Button>

  <br />

  <Link
    href="https://images-prod.healthline.com/hlcmsresource/images/AN_images/tomatoes-1296x728-feature.jpg"
    target="_blank"
    icon="icon-park-solid:tomato"
    type="danger"
  >
    Tomato
  </Link>

  <br />

  <RadioList
    buttons
    :value="kamal"
    :options="['tazhu', 'kamal', 'ilmu', 'radjab', 'kurban']"
    theme="success"
    @change="(v) => (kamal = v)"
  />

  <br />

  <div>
    <Checkbox button theme="warning" v-model="isAllFruits">
      Выбрать все
    </Checkbox>
  </div>

  <br />

  <CheckList
    :selected="fruits"
    :options="['Яблоко', 'Апельсин', 'Банан', 'Ананас', 'Гранат']"
    :test="(fruit: string) => isAllFruits || fruits.includes(fruit)"
    :disabled="() => isAllFruits"
    theme="warning"
    @change="fruitsSelectToggle"
  />

  <br />

  <div style="width: 30vw">
    <Input
      v-model="kamal"
      icon="fluent-emoji-high-contrast:cucumber"
      placeholder="Что-нибудь..."
    />

    <br />

    <Input v-model="kamal" :limit="10" />

    <br />

    <Input v-model="kamal" clearable theme="success" placeholder="Очищаемый">
      <template #prepend>
        <span style="white-space: nowrap">Type something here -></span>
      </template>

      <template #append>
        <Button type="success" icon="material-symbols:search" rounded faded />
      </template>
    </Input>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

// Components
import Button from "@/components/Button/index.vue";
import Link from "@/components/Link/index.vue";
import RadioList from "@/components/RadioList/index.vue";
import Checkbox from "@/components/Checkbox/index.vue";
import CheckList from "@/components/Checkbox/list.vue";
import Input from "@/components/Input/index.vue";

interface MainPage {
  kamal: any;
  isAllFruits: boolean;
  fruits: Array<string>;
}

export default defineComponent({
  name: "App",
  components: {
    Button,
    Link,
    RadioList,
    Checkbox,
    CheckList,
    Input,
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
