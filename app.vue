<script setup>
import { ref } from "vue";

import VueDatePicker from "@vuepic/vue-datepicker";
import "@vuepic/vue-datepicker/dist/main.css";
import { Result } from "postcss";

const listItems = ref([
  {
    name: "USA",
    cargo: "Crud oil",
    f: "FOB",
    bl: "BLOO1",
    quantity: "100000",
    ld: "5000",
    term: "CIF",
    dem: "12000",
    des: "12000",
    allowed: "5",
    used: "USA",
    deduction: "USA",
    balance: "USA",
    from: "USA",
    to: "USA",

    activities: [],
  },
  {
    name: "Brazil",
    cargo: "Crud oil",
    f: "FOB",
    bl: "BLOO1",
    quantity: "100000",
    ld: "5000",
    term: "CIF",
    dem: "12000",
    des: "12000",
    allowed: "5",
    used: "USA",
    deduction: "USA",
    balance: "USA",
    from: "USA",
    to: "USA",

    activities: [],
  },
  {
    name: "Singapore",
    cargo: "Crud oil",
    f: "FOB",
    bl: "BLOO1",
    quantity: "100000",
    ld: "5000",
    term: "CIF",
    dem: "12000",
    des: "12000",
    allowed: "5",
    used: "USA",
    deduction: "USA",
    balance: "USA",
    from: "USA",
    to: "USA",

    activities: [],
  },
]);

const selected = ref({});

const selectedItem = computed(() => {
  let result = listItems.value.find((item) => {
    return item.name === selected.value.name;
  });

  return result || [];
});

const addRow = () => {
  let selectedPortIndexx = listItems.value.findIndex((item) => {
    return item.name === selected.value.name;
  });

  if (selectedPortIndexx === -1) {
    alert("Please select a port to add activity");
    return;
  }

  const newRow = {
    day: "",
    type: "0",
    from: listItems.value[selectedPortIndexx].activities.length
      ? listItems.value[selectedPortIndexx].activities[0].to
      : "",
    duration: "",
    day: "",
    percent: "0",
    to: "",
    remarks: "",
    deduction: "",
  };

  listItems.value[selectedPortIndexx].activities.unshift(newRow);
};

const from = computed(() => {
  let selectedPortIndex = listItems.value.findIndex((item) => {
    return item.name === selected.value.name;
  });

  return selectedPortIndex > -1
    ? listItems.value[selectedPortIndex].activities.map((el) => el.from)
    : [""];
});

const activities = computed(() => {
  let selectedPortIndex = listItems.value.findIndex((item) => {
    return item.name === selected.value.name;
  });

  return selectedPortIndex > -1
    ? listItems.value[selectedPortIndex].activities
    : [];
});

const selectedPortIndex = computed(() => {
  return selected.value.name
    ? listItems.value.findIndex((item) => item.name === selected.value.name)
    : -1;
});

const selectedActivityIndex = ref(-1);
watch(
  () => activities.value,
  (newVal) => {
    if (
      selectedActivityIndex.value > -1 &&
      listItems.value[selectedPortIndex.value].activities[
        selectedActivityIndex.value
      ]?.from &&
      listItems.value[selectedPortIndex.value].activities[
        selectedActivityIndex.value
      ]?.to
    ) {
      let timeDistance =
        new Date(
          listItems.value[selectedPortIndex.value].activities[
            selectedActivityIndex.value
          ].to,
        ).getTime() -
        new Date(
          listItems.value[selectedPortIndex.value].activities[
            selectedActivityIndex.value
          ].from,
        ).getTime();

      let timeDistanceDeduction =
        (timeDistance *
          +listItems.value[selectedPortIndex.value].activities[
            selectedActivityIndex.value
          ].percent) /
        100;

      let day, houre, min;

      day = Math.floor(timeDistance / 86400000);

      houre = Math.floor((timeDistance % 86400000) / 3600000);

      min = Math.floor(((timeDistance % 86400000) % 3600000) / 60000);

      let dayDeduction, houreDeduction, minDeduction;

      dayDeduction = Math.floor(timeDistanceDeduction / 86400000);

      houreDeduction = Math.floor((timeDistanceDeduction % 86400000) / 3600000);

      minDeduction = Math.floor(
        ((timeDistanceDeduction % 86400000) % 3600000) / 60000,
      );

      listItems.value[selectedPortIndex.value].activities[
        selectedActivityIndex.value
      ].duration =
        day > 0
          ? `${day} days ${houre} hours ${min} minutes`
          : houre > 0
          ? `${houre} hours ${min} minutes`
          : min > 0
          ? `${min} minutes`
          : "0 minutes";

      listItems.value[selectedPortIndex.value].activities[
        selectedActivityIndex.value
      ].deduction =
        dayDeduction > 0
          ? `${dayDeduction} days ${houreDeduction} hours ${minDeduction} minutes`
          : houreDeduction > 0
          ? `${houreDeduction} hours ${minDeduction} minutes`
          : minDeduction > 0
          ? `${minDeduction} minutes`
          : "0 minutes";
    } else if (
      selectedActivityIndex.value > -1 &&
      (listItems.value[selectedPortIndex.value].activities[
        selectedActivityIndex.value
      ]?.from ||
        listItems.value[selectedPortIndex.value].activities[
          selectedActivityIndex.value
        ]?.to)
    ) {
      listItems.value[selectedPortIndex.value].activities[
        selectedActivityIndex.value
      ].duration = 0;
      listItems.value[selectedPortIndex.value].activities[
        selectedActivityIndex.value
      ].deduction = 10;
    }
  },
  { deep: true },
);
</script>

<template>
  <div class="p-3">
    <div class="bg-white rounded-xl p-4 mb-4">
      <div class="flex items-center gap-x-2 mb-5">
        <div class="h-4 w-1 bg-cyan-600"></div>

        <span class="font-bold"> Lay Times </span>
      </div>

      <div class="overflow-auto">
        <table class="w-full text-sm">
          <thead>
            <tr class="bg-slate-300 divide-x-2 overflow-auto">
              <th class="py-1 rounded-tl-lg">Port Name</th>
              <th class="py-1">Cargo</th>
              <th class="py-1">F</th>
              <th class="py-1">BL Code</th>
              <th class="py-1">Quantity</th>
              <th class="py-1">L/D Rate</th>
              <th class="py-1">Term</th>
              <th class="py-1">Dem Rate</th>
              <th class="py-1">Des Rate/D</th>
              <th class="py-1">Allowed</th>
              <th class="py-1">Used</th>
              <th class="py-1">Deduction</th>
              <th class="py-1">Balance</th>
              <th class="py-1">Laycan Form</th>
              <th class="py-1 rounded-tr-lg">Laycan To</th>
            </tr>
          </thead>

          <tbody>
            <tr
              v-for="item in listItems"
              :key="item.name"
              @click="selected = item"
              class="border-b cursor-pointer"
              :class="{
                'bg-blue-100': selected?.name === item.name,
              }"
            >
              <td class="py-1.5 pl-1">
                {{ item.name }}
              </td>
              <td class="py-1.5">
                {{ item.cargo }}
              </td>
              <td class="py-1.5">
                {{ item.f }}
              </td>
              <td class="py-1.5">
                {{ item.bl }}
              </td>
              <td class="py-1.5">
                {{ item.quantity }}
              </td>
              <td class="py-1.5">
                {{ item.ld }}
              </td>
              <td class="py-1.5">
                {{ item.term }}
              </td>
              <td class="py-1.5">
                {{ item.dem }}
              </td>
              <td class="py-1.5">
                {{ item.des }}
              </td>
              <td class="py-1.5">
                {{ item.allowed }}
              </td>
              <td class="py-1.5">
                {{ item.used }}
              </td>
              <td class="py-1.5">
                {{ item.deduction }}
              </td>
              <td class="py-1.5">
                {{ item.balance }}
              </td>
              <td class="py-1.5">
                {{ item.from }}
              </td>
              <td class="py-1.5 pr-1">
                {{ item.to }}
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <div class="bg-white p-4 rounded-xl">
      <div class="flex items-center justify-between mb-5">
        <div class="flex items-center gap-x-2 mb-5">
          <div class="h-4 w-1 bg-cyan-600"></div>

          <span class="font-bold"> Port Activity </span>
        </div>
        <button
          @click="addRow"
          class="flex items-center gap-x-2 w-fit px-3 py-0.5 border rounded-lg"
        >
          <span>+</span>

          <span> Add New </span>
        </button>
      </div>

      <div class="">
        <table class="w-full text-sm">
          <thead>
            <tr class="bg-slate-300 divide-x-2">
              <th class="py-1 rounded-tl-lg">Day</th>
              <th class="py-1">Activity Type</th>
              <th class="py-1">From Date & Time</th>
              <th class="py-1">Duration</th>
              <th class="py-1">%</th>
              <th class="py-1">To Date & Time</th>
              <th class="py-1">Remarks</th>
              <th class="py-1">Deductions</th>
              <th class="py-1 rounded-tr-lg"></th>
            </tr>
          </thead>

          <tbody>
            <tr
              v-for="(item, index) in selectedItem.activities"
              :key="item.name"
              class="border-b"
              @click="selectedActivityIndex = index"
            >
              <td>
                <input
                  type="text"
                  class="w-full border focus:outline-none px-2 py-1"
                  v-model="item.day"
                />
              </td>
              <td>
                <select
                  class="w-full border focus:outline-none px-2 py-1"
                  v-model="item.type"
                >
                  {{
                    item.type
                  }}
                  <option value="0">Unknown</option>
                  <option value="1">Option 1</option>
                  <option value="2">Option 2</option>
                </select>
              </td>
              <td>
                <VueDatePicker
                  @open="selectedActivityIndex = index"
                  v-model="item.from"
                  placeholder="Choose the date"
                />
              </td>
              <td>
                {{ item.duration }}
              </td>
              <td>
                <select
                  class="w-full border focus:outline-none px-2 py-1"
                  v-model="item.percent"
                >
                  <option value="0">0%</option>
                  <option value="50">50%</option>
                  <option value="100">100%</option>
                </select>
              </td>
              <td>
                <VueDatePicker
                  @open="selectedActivityIndex = index"
                  v-model="item.to"
                  placeholder="Choose the date"
                />
              </td>

              <td>Burthing complited</td>
              <td>
                {{ item.deduction }}
              </td>
              <td>jjjjj</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>
