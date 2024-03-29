<template>
  <h1>Day planner</h1>
  <div class="dayplanner" ref="testHtml">
    <div class="dayplanner-row">
      <div class="dayplanner-col">
        <h3>None selected</h3>
        <draggable
          class="dayplanner-group"
          ghost-class="dayplanner-ghost"
          chosen-class="dayplanner-choosen"
          :list="itemsAvailable"
          :group="{
            name: 'dayplanner',
            pull: 'clone',
            put: false,
            sort: false,
          }"
          itemKey="id"
        >
          <template #item="{ element }">
            <div class="dayplanner-item dayplanner-item--grab">
              {{ element.description }}
            </div>
          </template>
        </draggable>
      </div>

      <div class="dayplanner-col">
        <h3>Selected</h3>
        <draggable
          tag="ol"
          :list="itemsSelected"
          class="dayplanner-group"
          ghost-class="dayplanner-ghost"
          chosen-class="dayplanner-choosen"
          group="dayplanner"
          handle=".handle"
          @change="persist"
          item-key="id"
        >
          <template #item="{ element, index }">
            <li class="dayplanner-item">
              <span class="fa fa-align-justify handle"></span>
              <span class="text">{{ element.description }} </span>
              <input
                type="text"
                class="form-control"
                v-model="element.description"
              />
              <span
                class="fa fa-times close"
                @click="removeFromSelected(index)"
              ></span>
            </li>
          </template>
        </draggable>
      </div>
    </div>
  </div>
  <h4>Items available</h4>
  <code>{{ itemsAvailable }}</code>
  <hr />
  <h4>Items selected</h4>
  <code>{{ itemsSelected }}</code>
  <hr />
  <button @click="generatePdf()">generate PDF</button>
</template>

<script>
import draggable from "vuedraggable";
import jsPDF from "jspdf";
import images from "../store/images";

export default {
  name: "DayPlanner",
  components: {
    draggable,
  },
  data() {
    return {
      itemsAvailable: images,
      itemsDeleted: [],
      itemsSelected: [],
    };
  },
  computed: {
    myList: {
      get() {
        return JSON.parse(localStorage.getItem("myList")) || [];
      },
      set(value) {
        localStorage.setItem("myList", JSON.stringify(value));
      },
    },
  },
  methods: {
    generatePdf: function () {
      const doc = new jsPDF();
      this.itemsSelected.forEach(async (item) => {
        doc.addImage(
          require(`@/assets/images/${item.filename}`),
          "JPEG",
          15,
          30,
          180,
          120
        );
        doc.addPage();
      });
      // Save the PDF
      doc.save("test.pdf");
      // Return the PDF as a blob
      doc.output("blob");
    },
    persist: function () {
      localStorage.itemsSelected = JSON.stringify(this.itemsSelected);
    },
    removeFromSelected(index) {
      this.itemsSelected.splice(index, 1);
      this.persist();
    },
  },
  mounted() {
    if (window.localStorage.itemsSelected) {
      this.itemsSelected = JSON.parse(window.localStorage.itemsSelected);
    }
  },
};
</script>


<style scoped lang="css">
:root {
  --color-primary: #00bcd4;
  --color-secondary: #ff4081;
  --color-tertiary: #ff4081;
  --color-gray: #f5f5f5;
  --color-gray-light: #eaeaea;
  --color-gray-dark: #9b9b9b;
  --color-gray-light-dark: #737373;
  --color-gray-light-light: #d3d3d3;
  --font-family: "Roboto", sans-serif;
  --font-size-base: 16px;
  --font-size-sm: 12px;
  --font-size-lg: 20px;
  --font-size-xlg: 24px;
  --font-size-xxlg: 32px;
  --padding-base: 16px;
  --padding-sm: 8px;
  --padding-lg: 16px;
  --padding-xlg: 24px;
  --padding-xxlg: 32px;
  --padding-xxxlg: 48px;
}
.dayplanner {
  border: red solid 1px;
}
.dayplanner-row {
  display: grid;
  grid-auto-flow: column;
  gap: var(--padding-base);
}
.dayplanner-col {
  flex: 1;
  height: 100%;
  border: 1px solid var(--color-gray);
}

.dayplanner-group {
  border: 1px solid var(--color-gray-light);
  min-height: 100px;
  min-width: 100px;
  background-color: var(--color-gray-light-light);
  padding: var(--padding-base) var(--padding-base) var(--padding-xxxlg);
}
.dayplanner-item {
  cursor: draggable;
  padding: 10px;
  border: 1px solid #ccc;
  margin: 5px;
  background-color: #eee;
  position: relative;
}
.dayplanner-item--grab {
  cursor: grab;
}
.dayplanner-choosen,
.dayplanner-ghost,
.dayplanner-item--grab:active {
  cursor: grabbing !important;
}
.dayplanner-choosen {
  border: #ff4081 solid 1px;
}
.dayplanner-item > input {
  width: 6em;
  line-height: 1;
  height: 1.8em;
  padding: 0.5em;
  border: 1px solid #ccc;
  color: #444;
  position: absolute;
  right: 1rem;
  top: 0;
  bottom: 0;
}
.dayplanner-ghost {
  background-color: #ccc;
}

.button {
  margin-top: 35px;
}
ol {
  list-style: none;
}
ol > li {
  position: relative;
  display: block;
  padding: 0.5rem;
  margin-left: 0.5rem;
  margin-right: 0.5rem;
  border-radius: 0.25rem;
  background-color: #eee;
  border: 1px solid #ccc;
}
.handle::before:active {
  cursor: grabbing;
}
.handle::before {
  position: absolute;
  top: 0;
  left: 0;
  content: "☰";
  cursor: grab;
  padding-top: 8px;
  padding-bottom: 8px;
  border: magenta solid 1px;
  width: 2rem;
  height: 2rem;
}
.close::after {
  position: absolute;
  top: 0;
  right: 0;
  padding-top: 8px;
  padding-bottom: 8px;
  content: "⌫";
  width: 2rem;
  height: 2rem;
  border: red solid 1px;
}
input {
  display: inline-block;
  width: 50%;
}
.text {
  margin: 20px;
}
</style>
