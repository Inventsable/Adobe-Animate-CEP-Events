<template>
  <Wrapper class="home-wrapper">
    <Anno>Last Event: {{ lastEvent || "None" }}</Anno>
    <div
      class="event-wrapper"
      v-for="(value, key) in _data"
      :key="key"
      v-if="key !== 'lastEvent' && !/count/i.test(key)"
      :style="checkIfActive(key)"
    >
      <Input-Scroll read-only :label="key" :value="getValue(key)" />
      <span>{{ value }}</span>
    </div>
  </Wrapper>
</template>

<script>
const CSI = new CSInterface();

export default {
  data: () => ({
    lastEvent: null,
    documentChanged: null,
    documentChangedCount: 0,
    timelineChanged: null,
    timelineChangedCount: 0,
    documentSaved: null,
    documentSavedCount: 0,
    documentOpened: null,
    documentOpenedCount: 0,
    documentClosed: null,
    documentClosedCount: 0,
    documentNew: null,
    documentNewCount: 0,
    layerChanged: null,
    layerChangedCount: 0,
    frameChanged: null,
    frameChangedCount: 0,
    selectionChanged: null,
    selectionChangedCount: 0,
    mouseMove: null,
    mouseMoveCount: 0,
  }),
  mounted() {
    // Get all the keys of this.data, do a callback on every one
    Object.keys(this._data).forEach((key) => {
      // Unless it's a lastEvent or includes "Count" in it's name
      if (key !== "lastEvent" && !/count/i.test(key)) {
        // Enumerate through to create event listeners for each
        CSI.addEventListener(`com.adobe.events.flash.${key}`, (evt) => {
          // Record it as the most recent event
          this.lastEvent = key;
          // Assign the returned value to this[eventName]
          this[key] = evt;
          // Increment it's counter by one
          this[`${key}Count`] = this[`${key}Count`] + 1;
        });
      }
    });
  },
  methods: {
    getValue(key) {
      // Vue doesn't like doing this inline in a template apparently
      return this[`${key}Count`];
    },
    checkIfActive(name) {
      return name == this.lastEvent
        ? `color: var(--color-selection) !important;`
        : "";
    },
  },
};
</script>

<style>
.home-wrapper {
  min-height: 580;
  min-width: 300px;
}
</style>
