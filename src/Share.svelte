<script>
  import secrets from "secrets.js-grempe";
  import CopyToClipboard from "svelte-copy-to-clipboard";
  
  import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();

  import Slider from "@smui/slider";
  import Textfield from "@smui/textfield";
  import Icon from "@smui/textfield/icon/index";
  import FormField from "@smui/form-field";
  import Button, { Group, Label } from "@smui/button";

  export let defaultNumberOfShare;
  let shares = [];

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

  const share = function () {
    let hex = secrets.str2hex(sharing.secret);
    shares = secrets.share(hex, sharing.numShares, sharing.numRecover);
  };

  const handleSuccessfullyCopied = () => {
    dispatch("alert", "Succesfully copied to the clipboard");
  };

  const handleFailedCopy = () => {
    dispatch("alert", "Cannot copy to the clipboard");
  };
</script>

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
