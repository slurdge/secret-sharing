<script>
  import secrets from "secrets.js-grempe";

  import Textfield, { Input, Textarea } from "@smui/textfield";
  import Icon from "@smui/textfield/icon/index";
  import FormField from "@smui/form-field";
  import Button, { Group, GroupItem, Label } from "@smui/button";
  import IconButton from "@smui/icon-button";
  import Fab from "@smui/fab";

  export let defaultNumberOfShare;
  export let inputs = [];

  const addShare = () => {
    inputs = [...inputs, { password: "", visible: false }];
  };

  for (let i = 0; i < defaultNumberOfShare; ++i) {
    addShare();
  }

  let combined = "";

  const combine = function () {
    let shares = inputs.map((input) => input.password);
    shares = shares.filter((share) => share != "");
    let combinedHex = secrets.combine(shares);
    combined = secrets.hex2str(combinedHex);
  };

  const toggleVisibility = function (input) {
    input.visible = !input.visible;
    inputs = inputs;
    sharing = sharing;
  };
</script>

<div class="container" style="display: grid">
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
  <Fab
    mini
    color="primary"
    style="margin-top: -15px; justify-self: center;"
    on:click={addShare}>
    +
  </Fab>
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
