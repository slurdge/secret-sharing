<script>
  import secrets from "secrets.js-grempe";
  import CopyToClipboard from "svelte-copy-to-clipboard";

  import Tab, { Icon as TabIcon, Label as TabLabel } from "@smui/tab";
  import TabBar from "@smui/tab-bar";
  import Snackbar, { Actions, Label as SnackbarLabel } from "@smui/snackbar";
  import IconButton from "@smui/icon-button";
  import Icon from "@smui/textfield/icon/index";

  import About from "./About.svelte";
  import Share from "./Share.svelte";
  import Combine from "./Combine.svelte";

  const defaultNumberOfShare = 5;

  let inputs, sharing;

  const tabs = [
    { key: 0, label: "Share" },
    { key: 1, label: "Combine" },
    { key: 2, label: "About" },
  ];
  let activeTab = tabs[1];
  let alertBar;
  let alertText = "";

  const alert = (event) => {
    alertText = event.detail;
    alertBar.open();
  };
</script>

<style>

</style>

<main style="margin-left: 5%; margin-right: 5%">
  <h1 class="display-4" style="text-align:center">Secret Sharing</h1>
  <TabBar {tabs} let:tab bind:active={activeTab} style="margin-bottom: 7px;">
    <Tab {tab}>
      <TabLabel>{tab.label}</TabLabel>
    </Tab>
  </TabBar>
  {#if activeTab.key == 1}
    <Combine {defaultNumberOfShare} bind:inputs />
  {:else if activeTab.key == 0}
    <Share {defaultNumberOfShare} on:alert={alert} bind:sharing />
  {:else if activeTab.key == 2}
    <About share={defaultNumberOfShare} />
  {/if}
  <Snackbar bind:this={alertBar} labelText={alertText}>
    <SnackbarLabel />
    <Actions>
      <IconButton>
        <Icon style="font-size: 100%;" class="close" title="Dismiss" />
      </IconButton>
    </Actions>
  </Snackbar>
</main>
