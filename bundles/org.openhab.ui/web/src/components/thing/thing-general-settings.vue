<template>
  <f7-block class="config-sheet no-margin" style="margin-bottom: 0">
    <f7-col>
      <f7-block width="100" class="parameter-group no-margin">
        <f7-row>
          <f7-col>
            <f7-list inline-labels no-hairlines-md class="no-margin">
              <f7-list-input label="Unique ID" v-if="createMode" type="text" placeholder="Required" :value="thing.ID"
                             @input="changeUID" info="Note: cannot be changed after the creation"
                             required validate pattern="[A-Za-z0-9_\-]+" error-message="Required. A-Z,a-z,0-9,_,- only" />
              <f7-list-input label="Identifier" type="text" placeholder="Name" :value="thing.UID" disabled />
              <f7-list-input label="Label" type="text" :disabled="!ready || readOnly" placeholder="e.g. My Thing" :value="thing.label"
                             @input="thing.label = $event.target.value; $emit('updated')" required validate />
              <f7-list-input label="Location" type="text" :disabled="!ready || readOnly" placeholder="e.g. Kitchen" :value="thing.location"
                             @input="thing.location = $event.target.value; $emit('updated')" clear-button />
            </f7-list>
            <f7-block-title v-if="ready && thingType.supportedBridgeTypeUIDs.length">
              Parent Bridge
            </f7-block-title>
            <f7-block-footer v-if="ready && thingType.supportedBridgeTypeUIDs.length && !thing.bridgeUID"
                             class="padding-left padding-right">
              This type of Thing needs to be associated to a working Bridge to function properly.
            </f7-block-footer>
            <f7-list v-if="ready && thingType.supportedBridgeTypeUIDs.length" inline-labels no-hairlines-md>
              <thing-picker
                title="Bridge" name="bridge" :value="thing.bridgeUID"
                @input="updateBridge"
                :filterType="thingType.supportedBridgeTypeUIDs" />
            </f7-list>
          </f7-col>
        </f7-row>
      </f7-block>
    </f7-col>
  </f7-block>
</template>

<script>
import ThingPicker from '@/components/config/controls/thing-picker.vue'

export default {
  props: ['thing', 'thingType', 'createMode', 'ready', 'readOnly'],
  components: {
    ThingPicker
  },
  methods: {
    computedThingUid () {
      return (this.thing.bridgeUID)
        ? [this.thing.thingTypeUID, this.thing.bridgeUID.substring(this.thing.bridgeUID.lastIndexOf(':') + 1), this.thing.ID].join(':')
        : [this.thing.thingTypeUID, this.thing.ID].join(':')
    },
    changeUID (event) {
      this.thing.ID = event.target.value
      if (this.createMode) this.thing.UID = this.computedThingUid()
    },
    updateBridge (value) {
      this.thing.bridgeUID = value
      this.$emit('updated')
      if (this.createMode) this.thing.UID = this.computedThingUid()
    }
  }
}
</script>
