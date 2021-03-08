<template>
  <Page class="page">
    <ActionBar class="action-bar">
      <!--
        Use the NavigationButton as a side-drawer button in Android
        because ActionItems are shown on the right side of the ActionBar
        -->
      <NavigationButton
        ios:visibility="collapsed"
        icon="res://menu"
        @tap="onDrawerButtonTap"
      />
      <!--
        Use the ActionItem for IOS with position set to left. Using the
        NavigationButton as a side-drawer button in iOS is not possible,
        because its function is to always navigate back in the application.
        -->
      <ActionItem
        icon="res://menu"
        android:visibility="collapsed"
        @tap="onDrawerButtonTap"
        ios.position="left"
      />
      <Label class="action-bar-title" text="Search" />
    </ActionBar>

    <StackLayout>
      <RadDataForm
        ref="dataform"
        :source="person"
        :metadata="personMetadata"
        :validationMode="validationMode"
        commitMode="Manual"
      >
      </RadDataForm>
      <Button
        text="Validate manually"
        horizontalAlignment="stretch"
        @tap="onValidateTap()"
      ></Button>
    </StackLayout>
  </Page>
</template>

<script>
import * as utils from "~/shared/utils";
import SelectedPageService from "../shared/selected-page-service";
import { DataFormValidationMode } from "nativescript-ui-dataform";

export default {
  mounted() {
    SelectedPageService.getInstance().updateSelectedPage("Search");
  },
  computed: {
    message() {
      return "<!-- Page content goes here -->";
    },
  },
  data() {
    return {
      text: "",
      validationMode: DataFormValidationMode.Immediate,
      person: {
        username: "",
        password: "",
      },
      personMetadata: {
        isReadOnly: false,
        propertyAnnotations: [
          {
            name: "username",
            displayName: "Nick",
            index: 0,
            validators: [
              { name: "NonEmpty" },
              { name: "MaximumLength", params: { length: 10 } },
            ],
          },
          {
            name: "password",
            displayName: "Password",
            index: 2,
            validators: [
              {
                name: "NonEmpty",
              },
            ],
          },
        ],
      },
    };
  },
  methods: {
    onDrawerButtonTap() {
      utils.showDrawer();
    },
    onImmediateTap() {
      this.validationMode = DataFormValidationMode.Immediate;
    },
    onOnLostFocusTap() {
      this.validationMode = DataFormValidationMode.OnLostFocus;
    },
    onManualTap() {
      this.validationMode = DataFormValidationMode.Manual;
    },
    onValidateTap() {
      this.$refs.dataform.validateAll().then((result) => {
        this.$refs.dataform.commitAll();
        console.log(this.person);
        console.log(`Validation result: ${result}`);
      });
    },
  },
};
</script>

<style scoped lang="scss">
// Start custom common variables
@import "~@nativescript/theme/scss/variables/blue";
// End custom common variables

// Custom styles
</style>
