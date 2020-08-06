<script>
  import Paper, { Title, Content } from "@smui/paper";
  import Button, { Group, Label } from "@smui/button";
  import Icon from "@smui/textfield/icon/index";

  export let share;
</script>

<Paper color="primary" style="margin-top: 15px;">
  <Title>Secret Sharing</Title>
  <Content>
    <p>
      This application allows you to share a secret between a certain number of
      persons. You can specify how many persons are needed to recover the
      secret. For example, you could share it with a group of {share} people
      while requesting that {Math.floor(share / 2)} person are needed to recover
      it. It uses the perfect secret sharing found by
      <a href="https://en.wikipedia.org/wiki/Shamir's_Secret_Sharing">Shamir</a>
      .
    </p>
  </Content>
  <Title>Security</Title>
  <Content>
    <p>
      This application runs entirely in your browser. Great effort has been made
      that there are only one initial connection, to download the page. You can
      convince yourself by monitoring the Network usage.
    </p>
  </Content>
  <Title>Stack</Title>
  <Content>
    <p>
      Built with ❤️ by
      <a href="https://www.slurdge.org/">slurdge</a>
      on the following technologies
    </p>
    <Group
      variant="outlined"
      style="display: flex; margin-top: 10px; margin-bottom: 10px;">
      <Button
        variant="unelevated"
        color="secondary"
        style="flex-grow: 2;"
        href="https://svelte.dev/">
        <Label>
          <Icon class="svelte" style="font-size: 100%" />
          Svelte
        </Label>
      </Button>
      <Button
        variant="unelevated"
        color="secondary"
        style="flex-grow: 3;"
        href="https://github.com/hperrin/svelte-material-ui">
        <Label>
          <Icon class="materialdesign" style="font-size: 100%" />
          Material
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
