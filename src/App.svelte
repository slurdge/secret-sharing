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
  import Paper, { Title, Subtitle, Content } from "@smui/paper";

  let inputs = [];
  const defaultNumberOfShare = 5;
  for (let i = 0; i < defaultNumberOfShare; ++i) {
    inputs[i] = { ...{ password: "", visible: false } };
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

  const handleSuccessfullyCopied = (e) => {
    alertText = "Succesfully copied to the clipboard";
    alertBar.open();
  };

  const handleFailedCopy = () => {
    alertText = "Cannot copy to the clipboard";
    alertBar.open();
  };
</script>

<style>

</style>

<main style="margin-left: 5%; margin-right: 5%">
  <h1 class="display-4" style="text-align:center">Secret Sharing</h1>
  <TabBar {tabs} let:tab bind:active={activeTab} style="margin-bottom: 7px;">
    <!-- Notice that the `tab` property is required! -->
    <Tab {tab}>
      <Label>{tab.label}</Label>
    </Tab>
  </TabBar>
  {#if activeTab.key == 1}
    <div class="container">
      {#each inputs as input, index}
        <Textfield
          variant="outlined"
          type={input.visible ? 'textarea' : 'password'}
          withTrailingIcon
          bind:value={input.password}
          label={'Share ' + (index + 1)}
          style="width: 100%; margin-top: 10px; margin-bottom: 10px;">
          <Icon
            class={input.visible ? 'visibility' : 'visibility_off'}
            role="button"
            on:click={toggleVisibility(input)} />
        </Textfield>
      {/each}
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
        style="margin-top: 20px;"
        readonly
        fullwidth
        textarea
        bind:value={combined}
        label="Recovered" />
    </div>
  {:else if activeTab.key == 0}
    <Textfield
      variant="outlined"
      textarea
      withTrailingIcon
      bind:value={sharing.secret}
      label="Secret"
      style="width: 100%; margin-top: 10px; margin-bottom: 10px; ">
      <Icon
        class="material-icons"
        role="button"
        on:click={toggleVisibility(sharing)} />
    </Textfield>
    <Label>Total number of shares: {sharing.numShares}</Label>
    <FormField align="end" style="display: flex;">
      <Slider
        bind:value={sharing.numShares}
        min={2}
        max={32}
        step={1}
        discrete
        displayMarkers />
      <span
        slot="label"
        style="padding-right: 12px; width: max-content; display: block;">
        {sharing.numShares}
      </span>

    </FormField>
    <Label>Number of shares needed for recovery</Label>
    <FormField align="end" style="display: flex;">
      <Slider
        bind:value={sharing.numRecover}
        min={2}
        max={sharing.numShares}
        step={1}
        discrete
        displayMarkers />
      <span
        slot="label"
        style="padding-right: 12px; width: max-content; display: block;">
        {sharing.numRecover}
      </span>
    </FormField>
    <Group
      variant="outlined"
      style="display: flex; margin-top: 10px; margin-bottom: 10px;">
      <Button
        on:click={share}
        variant="unelevated"
        color="primary"
        style="flex-grow: 3;">
        <Label>Share</Label>
      </Button>
    </Group>
    {#each shares as share, index}
      <Textfield
        variant="outlined"
        type="textarea"
        withTrailingIcon
        value={share}
        label={'Share ' + (index + 1)}
        style="width: 100%; margin-top: 10px; margin-bottom: 10px;">
        <CopyToClipboard
          text={share}
          on:copy={handleSuccessfullyCopied}
          on:fail={handleFailedCopy}
          let:copy>
          <Icon class="content_copy" role="button" on:click={copy} />
        </CopyToClipboard>
      </Textfield>
    {/each}
  {:else if activeTab.key == 2}
    <Paper color="primary" style="margin-top: 15px;">
      <Title>Secret Sharing</Title>
      <Content>
        <p>
          This application allows you to share a secret between a certain number
          of persons. You can specify how many persons are needed to recover the
          secret. For example, you could share it with a group of {defaultNumberOfShare}
          people while requesting that {Math.floor(defaultNumberOfShare / 2)}
          person are needed to recover it. It uses the perfect secret sharing
          found by
          <a href="https://en.wikipedia.org/wiki/Shamir's_Secret_Sharing">
            Shamir
          </a>
          .
        </p>
      </Content>
      <Title>Security</Title>
      <Content>
        <p>
          This application runs entirely in your browser. Great effort has been
          made that there are only one initial connection, to download the page.
          You can convince yourself by monitoring the Network usage.
        </p>
      </Content>
      <Title>Stack</Title>
      <Content>
        <p>Built with ❤️ by <a href="https://www.slurdge.org/">slurdge</a> on the following technologies</p>
        <Group
          variant="outlined"
          style="display: flex; margin-top: 10px; margin-bottom: 10px;">
          <Button
            variant="unelevated"
            color="secondary"
            style="flex-grow: 3;"
            href="https://svelte.dev/">
            <Label>
              <Icon class="svelte" style="font-size: 100%"/>
              Svelte
            </Label>
          </Button>
          <Button
            variant="unelevated"
            color="secondary"
            style="flex-grow: 3;"
            href="https://github.com/hperrin/svelte-material-ui">
            <Label>
              <Icon class="materialdesign" style="font-size: 100%"/>
              Material-UI for Svelte
            </Label>
          </Button>
          <Button
            variant="unelevated"
            color="secondary"
            style="flex-grow: 3;"
            href="https://github.com/grempe/secrets.js">
            <Label>
              <Icon class="node-dot-js" style="font-size: 100%" />
              secret.js
            </Label>
          </Button>
        </Group>
      </Content>
    </Paper>
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
