<script lang="ts">
  import type { JsonLdProps } from './types';
  import type { Thing, WithContext } from 'schema-dts';


  type OmitContext<T> = Omit<T, '@context'>;

  const {schema, output = 'head'}: JsonLdProps = $props()

  const isValid = $derived(schema && typeof schema === 'object')

  const createSchema = (schema: JsonLdProps['schema']) => {
    const addContext = (context: OmitContext<Thing> | OmitContext<WithContext<Thing>>) => ({
      '@context': 'https://schema.org',
      ...context
    });

    return Array.isArray(schema)
      ? schema.map((context) => addContext(context as OmitContext<Thing>))
      : addContext(schema as OmitContext<WithContext<Thing>>);
  };

  const json = $derived(`${'<scri' + 'pt type="application/ld+json">'}${JSON.stringify(createSchema(schema))}${'</scri' + 'pt>'}`)
</script>

<svelte:head>
  {#if isValid && output === 'head'}
    {@html json}
  {/if}
</svelte:head>

{#if isValid && output === 'body'}
  {@html json}
{/if}
