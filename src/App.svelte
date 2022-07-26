<script lang="ts">
  import { onMount } from "svelte";
  import Fa from "svelte-fa/src/fa.svelte";
  import { faGithub } from "@fortawesome/free-brands-svg-icons";

  enum NumberType {
    Groups = "groups",
    ByGroup = "by group",
  }

  let input: HTMLInputElement;

  let inputText: string;

  let numberType: NumberType;

  let number: number = 3;

  let list: string[] = ["one", "two", "three", "four", "five"];

  let groups: string[][] = [];

  const add = () => {
    let addItems;
    addItems = inputText.split(",").map((text) => text.trim());
    list = [...list].concat(addItems);
  };

  const onKeyPress = (e) => {
    if (e.charCode === 13) add();
  };

  const deleteItem = (listIndex) => {
    list = list.slice(0, listIndex).concat(list.slice(listIndex + 1));
  };

  const clear = () => {
    if (groups.length !== 0) {
      groups = [];
    } else {
      list = [];
    }
  };

  const devide = () => {
    if (numberType === NumberType.Groups) {
      devideAsGroups();
    } else {
      devideAsNumberByGroup();
    }
  };

  const devideAsGroups = () => {
    let targets = [...list];

    const numberOfGroup = number;

    const actualNumberOfGroup = Math.min(list.length, numberOfGroup);

    groups = [];

    for (let i = 0; i < actualNumberOfGroup; i++) {
      groups.push([]);
    }

    while (targets.length !== 0) {
      for (const group of groups) {
        if (targets.length === 0) {
          break;
        }
        const targetIndex = randomIndex(targets.length);
        group.push(targets[targetIndex]);
        targets = targets
          .slice(0, targetIndex)
          .concat(targets.slice(targetIndex + 1));
      }
    }
  };

  const devideAsNumberByGroup = () => {
    let targets = [...list];

    const numberOfGroup = Math.ceil(list.length / number);

    const actualNumberOfGroup = Math.min(targets.length, numberOfGroup);

    groups = [];

    for (let i = 0; i < actualNumberOfGroup; i++) {
      groups.push([]);
    }

    for (const group of groups) {
      for (let i = 0; i < number && targets.length !== 0; i++) {
        const targetIndex = randomIndex(targets.length);
        group.push(targets[targetIndex]);
        targets = targets
          .slice(0, targetIndex)
          .concat(targets.slice(targetIndex + 1));
      }
    }
  };

  const randomIndex = (length: number) => {
    return Math.floor(Math.random() * length);
  };

  onMount(() => input.focus());
</script>

<main>
  <h2>
    Tekitoubun <a
      href="https://github.com/redfe/tekitoubun"
      target="_blank"
      rel="noopener noreferrer"><Fa icon={faGithub} /></a
    >
  </h2>
  <input
    bind:this={input}
    bind:value={inputText}
    on:keypress={onKeyPress}
    placeholder="separated by commas."
  />
  <button on:click={add} disabled={!inputText}>add</button>
  <p>
    <input type="number" min="1" bind:value={number} style:width="3.5rem" />
    <select bind:value={numberType}>
      <option value={NumberType.Groups}>groups</option>
      <option value={NumberType.ByGroup}>by group</option>
    </select>
    <button on:click={devide} disabled={list.length === 0}>divide</button>
  </p>
  <p>
    <button on:click={clear} disabled={list.length === 0}>clear</button>
  </p>
  <div class="output">
    <p>({list.length})</p>
    {#if groups.length === 0}
      <ul>
        {#each list as item, i}
          <li>
            <button class="delete" on:click={() => deleteItem(i)}>❌</button>
            <span>{item}</span>
          </li>
        {/each}
      </ul>
    {:else}
      <ul>
        {#each groups as items, i}
          <li>
            <p class="group-name">Group {i + 1}</p>
            <ul>
              {#each items as item}
                <li>{item}</li>
              {/each}
            </ul>
          </li>
        {/each}
      </ul>
    {/if}
  </div>
</main>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  button.delete {
    background: none;
    border: none;
    cursor: pointer;
    font-size: 0.75rem;
  }

  .output {
    text-align: left;
  }

  input,
  select,
  button {
    padding: 0.5rem;
    border-radius: 0.25rem;
    box-sizing: border-box;
    border: solid 1px rgba(0, 0, 0, 0.3);
  }

  button {
    background-color: silver;
  }

  button:not([disabled]):hover {
    opacity: 0.7;
  }

  h2 a {
    color: black;
  }
  h2 a:visited {
    color: black;
  }
</style>
