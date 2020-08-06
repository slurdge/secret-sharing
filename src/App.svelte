<script>
  import secrets from "secrets.js-grempe";
  import CopyToClipboard from "svelte-copy-to-clipboard";

  import Tab, { Icon as TabIcon, Label as TabLabel } from "@smui/tab";
  import TabBar from "@smui/tab-bar";
  import Slider from "@smui/slider";
  import Textfield, { Input, Textarea } from "@smui/textfield";
  import Icon from "@smui/textfield/icon/index";
  import FormField from "@smui/form-field";
  import Button, { Group, GroupItem, Label } from "@smui/button";
  import Snackbar, { Actions, Label as SnackbarLabel } from "@smui/snackbar";
  import IconButton from "@smui/icon-button";
  import Fab from '@smui/fab';

  import About from "./About.svelte"
  import Share from "./Share.svelte"

  let inputs = [];
  const defaultNumberOfShare = 5;

  const addShare = () => {
    inputs = [...inputs, {password: "", visible: false}];
  }

  for (let i = 0; i < defaultNumberOfShare; ++i) {
    addShare();
  }

  const tabs = [
    { key: 0, label: "Share" },
    { key: 1, label: "Combine" },
    { key: 2, label: "About" },
  ];
  let activeTab = tabs[1];
  let alertBar;
  let alertText = "";

  let combined = "";

  let sharing = {
    secret: "",
    visible: false,
    numShares: defaultNumberOfShare,
    numRecover: Math.floor(defaultNumberOfShare / 2),
  };

  $: sharing.numRecover =
    sharing.numRecover > sharing.numShares
      ? sharing.numShares
      : sharing.numRecover;

  const combine = function () {
    let shares = inputs.map((input) => input.password);
    shares = shares.filter((share) => share != "");
    let combinedHex = secrets.combine(shares);
    combined = secrets.hex2str(combinedHex);
  };

  let shares = [];

  const share = function () {
    let hex = secrets.str2hex(sharing.secret);
    shares = secrets.share(hex, sharing.numShares, sharing.numRecover);
  };

  const toggleVisibility = function (input) {
    input.visible = !input.visible;
    inputs = inputs;
    sharing = sharing;
  };

  const alert = (event) => {
    alertText = event.detail;
    alertBar.open();
  }

</script>

<style>

</style>

<main style="margin-left: 5%; margin-right: 5%">
  <h1 class="display-4" style="text-align:center">Secret Sharing</h1>
  <TabBar {tabs} let:tab bind:active={activeTab} style="margin-bottom: 7px;">
    <Tab {tab}>
      <Label>{tab.label}</Label>
    </Tab>
  </TabBar>
  {#if activeTab.key == 1}
    <div class="container" style="display: grid">
      {#each inputs as input, index}
        <Textfield
          variant="outlined"
          type={input.visible ? 'textarea' : 'password'}
          withTrailingIcon
          bind:value={input.password}
          label={'Share ' + (index + 1)}
          style="width: 100%; margin-top: 10px; margin-bottom: 10px;"
          >
          <Icon
            class={input.visible ? 'visibility' : 'visibility_off'}
            role="button"
            on:click={toggleVisibility(input)} />
        </Textfield>
      {/each}
      <Fab mini color="primary" style="margin-top: -15px; justify-self: center;" on:click={addShare}>+</Fab>
      <Group
        variant="outlined"
        style="display: flex; margin-top: 10px; margin-bottom: 10px;">
        <Button
          on:click={combine}
          variant="unelevated"
          color="primary"
          style="flex-grow: 3;">
          <Label>Recover combined</Label>
        </Button>
      </Group>
      <Textfield
        style="margin-top: 10px; margin-bottom: 10px;"
        readonly
        fullwidth
        textarea
        bind:value={combined}
        label="Recovered" />
    </div>
  {:else if activeTab.key == 0}
    <Share defaultNumberOfShare={defaultNumberOfShare} on:alert={alert}/>
  {:else if activeTab.key == 2}
    <About share={defaultNumberOfShare}/>
  {/if}
  <Snackbar bind:this={alertBar} labelText={alertText}>
    <Label />
    <Actions>
      <IconButton>
        <Icon style="font-size: 100%;" class="close" title="Dismiss" />
      </IconButton>
    </Actions>
  </Snackbar>
</main>
