<script lang="ts">
  import {
    createForm,
    Field,
    Form,
    reset,
    type SubmitHandler,
  } from "@formisch/svelte";
  import * as v from "valibot";
  import TextInput from "./form/text-input.svelte";
  import { Button } from "./ui/button";
  import { IconLoader } from "@tabler/icons-svelte";

  const AttachmentDownloaderSchema = v.object({
    url: v.pipe(
      v.string(),
      v.nonEmpty("pls provide a url"),
      v.url("it has to be a url"),
      v.check((value) => {
        try {
          const parsed = new URL(value);
          return parsed.hostname === "x.com";
        } catch {
          return false;
        }
      }, "you must provide an x.com link"),
    ),
  });

  const form = createForm({
    schema: AttachmentDownloaderSchema,
  });

  const submitForm: SubmitHandler<typeof AttachmentDownloaderSchema> = async (
    output,
  ) => {
    const fixedUrl = output.url.replace(
      /^https?:\/\/(www\.)?x\.com/,
      "https://d.fixupx.com",
    );

    const a = document.createElement("a");
    a.href = fixedUrl;
    a.target = "_blank";
    a.rel = "noopener noreferrer";

    document.body.appendChild(a);
    a.click();
    a.remove();

    reset(form);
  };
</script>

<p>attachment downloader</p>

<Form of={form} onsubmit={submitForm}>
  <Field of={form} path={["url"]}>
    {#snippet children(field)}
      <TextInput
        {...field.props}
        input={field.input}
        errors={field.errors}
        type="text"
        label="tweet url"
        placeholder="https://x.com/..."
        required
      />
    {/snippet}
  </Field>

  <br />

  <Button
    class="w-fit"
    disabled={!form.isDirty || !form.isValid || form.isSubmitting}
    type="submit"
  >
    {#if form.isSubmitting}
      <IconLoader class="animate-spin" />
    {/if}
    download
  </Button>
</Form>
