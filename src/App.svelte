<script>
  import secrets from "secrets.js-grempe";

  import Tab, { Icon as TabIcon, Label as TabLabel } from "@smui/tab";
  import TabBar from "@smui/tab-bar";

  import Textfield, { Input, Textarea } from "@smui/textfield";
  import Icon from "@smui/textfield/icon/index";

  import Button, { Group, GroupItem, Label } from "@smui/button";

  let inputs = [];
  for (let i = 0; i < 5; ++i) {
    inputs[i] = { ...{ password: "", visible: false } };
  }

  const tabs = [
    { key: 0, label: "Share" },
    { key: 1, label: "Combine" },
    { key: 2, label: "About" },
  ];
  let activeTab = tabs[1];

  let combined = "";

  const combine = function () {
    let shares = inputs.map((input) => input.password);
    shares = shares.filter((share) => share != '');
    let combinedHex = secrets.combine(shares);
    combined = secrets.hex2str(combinedHex);
  };


  const toggleVisibility = function (input) {
    input.visible = !input.visible;
    inputs = inputs;
  };
</script>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }

  .container {
    padding: 10px;
  }

</style>

<main>
  <h1 class="display-4" style="text-align:center">Secret Sharing</h1>
  <TabBar {tabs} let:tab bind:active={activeTab}>
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
          label={'Password ' + (index + 1)}
          class="customPassword"
          style="width: 100%; margin-top: 20px; ">
          <Icon
            class="material-icons"
            role="button"
            on:click={toggleVisibility(input)}>
            {input.visible ? 'visibility' : 'visibility_off'}
          </Icon>
        </Textfield>
      {/each}
      <Group variant="outlined" style="display: flex; margin-top: 20px;">
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
  {/if}
</main>
