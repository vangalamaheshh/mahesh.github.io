```python
import apache_beam as beam
from apache_beam.runners.interactive import interactive_runner
import apache_beam.runners.interactive.interactive_beam as ib
from apache_beam.options import pipeline_options
from datetime import timedelta
```


```python
ib.options.capture_duration = timedelta(seconds=60)
```


```python
options = pipeline_options.PipelineOptions()
```


```python
p = beam.Pipeline(interactive_runner.InteractiveRunner(), options=options)
```


```python
words = p | 'Create words' >> beam.Create(['Hello', 'there!', 'How', 'are', 'you?'])
```


```python
windowed_words = (words
   | "window" >> beam.WindowInto(beam.window.FixedWindows(10)))
```


```python
windowed_word_counts = (windowed_words
   | "count" >> beam.combiners.Count.PerElement())
```


```python
ib.show(windowed_word_counts, include_window_info=True, visualize_data = True)
```

<style>
.p-Widget.jp-OutputPrompt.jp-OutputArea-prompt:empty {
  padding: 0;
  border: 0;
}
.p-Widget.jp-RenderedJavaScript.jp-mod-trusted.jp-OutputArea-output:empty {
  padding: 0;
  border: 0;
}
</style>
<iframe id=facets_dive_0ea2265106b4d002f7fb235fa538a980 style="border:none" width="100%" height="600px"
  srcdoc='
    <script src="https://cdnjs.cloudflare.com/ajax/libs/webcomponentsjs/1.3.3/webcomponents-lite.js"></script>
    <link rel="import" href="https://raw.githubusercontent.com/PAIR-code/facets/1.0.0/facets-dist/facets-jupyter.html">
    <facets-dive sprite-image-width="64" sprite-image-height="64" id="facets_dive_0ea2265106b4d002f7fb235fa538a980" height="600"></facets-dive>
    <script>
      document.getElementById("facets_dive_0ea2265106b4d002f7fb235fa538a980").data = [{&quot;windowed_word_counts.0&quot;:&quot;Hello&quot;,&quot;windowed_word_counts.1&quot;:1,&quot;event_time&quot;:&quot;Min Timestamp&quot;,&quot;windows&quot;:&quot;Min Timestamp (10s)&quot;,&quot;pane_info&quot;:&quot;Pane 0&quot;},{&quot;windowed_word_counts.0&quot;:&quot;there!&quot;,&quot;windowed_word_counts.1&quot;:1,&quot;event_time&quot;:&quot;Min Timestamp&quot;,&quot;windows&quot;:&quot;Min Timestamp (10s)&quot;,&quot;pane_info&quot;:&quot;Pane 0&quot;},{&quot;windowed_word_counts.0&quot;:&quot;How&quot;,&quot;windowed_word_counts.1&quot;:1,&quot;event_time&quot;:&quot;Min Timestamp&quot;,&quot;windows&quot;:&quot;Min Timestamp (10s)&quot;,&quot;pane_info&quot;:&quot;Pane 0&quot;},{&quot;windowed_word_counts.0&quot;:&quot;are&quot;,&quot;windowed_word_counts.1&quot;:1,&quot;event_time&quot;:&quot;Min Timestamp&quot;,&quot;windows&quot;:&quot;Min Timestamp (10s)&quot;,&quot;pane_info&quot;:&quot;Pane 0&quot;},{&quot;windowed_word_counts.0&quot;:&quot;you?&quot;,&quot;windowed_word_counts.1&quot;:1,&quot;event_time&quot;:&quot;Min Timestamp&quot;,&quot;windows&quot;:&quot;Min Timestamp (10s)&quot;,&quot;pane_info&quot;:&quot;Pane 0&quot;}];
    </script>
  '>
</iframe>




<style>
.p-Widget.jp-OutputPrompt.jp-OutputArea-prompt:empty {
  padding: 0;
  border: 0;
}
.p-Widget.jp-RenderedJavaScript.jp-mod-trusted.jp-OutputArea-output:empty {
  padding: 0;
  border: 0;
}
</style>
<iframe id=facets_overview_0ea2265106b4d002f7fb235fa538a980 style="border:none" width="100%" height="600px"
  srcdoc='
    <script src="https://cdnjs.cloudflare.com/ajax/libs/webcomponentsjs/1.3.3/webcomponents-lite.js"></script>
    <link rel="import" href="https://raw.githubusercontent.com/PAIR-code/facets/1.0.0/facets-dist/facets-jupyter.html">
    <facets-overview id="facets_overview_0ea2265106b4d002f7fb235fa538a980"></facets-overview>
    <script>
      document.getElementById("facets_overview_0ea2265106b4d002f7fb235fa538a980").protoInput = "CuIKCgRkYXRhEAUa5QMKFndpbmRvd2VkX3dvcmRfY291bnRzLjAQAiLIAwqyAggFGAEgAS0AAIA/MqQCGhsJAAAAAAAA8D8RAAAAAAAA8D8hAAAAAAAA4D8aGwkAAAAAAADwPxEAAAAAAADwPyEAAAAAAADgPxobCQAAAAAAAPA/EQAAAAAAAPA/IQAAAAAAAOA/GhsJAAAAAAAA8D8RAAAAAAAA8D8hAAAAAAAA4D8aGwkAAAAAAADwPxEAAAAAAADwPyEAAAAAAADgPxobCQAAAAAAAPA/EQAAAAAAAPA/IQAAAAAAAOA/GhsJAAAAAAAA8D8RAAAAAAAA8D8hAAAAAAAA4D8aGwkAAAAAAADwPxEAAAAAAADwPyEAAAAAAADgPxobCQAAAAAAAPA/EQAAAAAAAPA/IQAAAAAAAOA/GhsJAAAAAAAA8D8RAAAAAAAA8D8hAAAAAAAA4D8gARAFGg8SBHlvdT8ZAAAAAAAA8D8aERIGdGhlcmUhGQAAAAAAAPA/JWZmhkAqZgoPIgR5b3U/KQAAAAAAAPA/ChUIARABIgZ0aGVyZSEpAAAAAAAA8D8KEggCEAIiA2FyZSkAAAAAAADwPwoSCAMQAyIDSG93KQAAAAAAAPA/ChQIBBAEIgVIZWxsbykAAAAAAADwPxrvBgoWd2luZG93ZWRfd29yZF9jb3VudHMuMRrUBgqyAggFGAEgAS0AAIA/MqQCGhsJAAAAAAAA8D8RAAAAAAAA8D8hAAAAAAAA4D8aGwkAAAAAAADwPxEAAAAAAADwPyEAAAAAAADgPxobCQAAAAAAAPA/EQAAAAAAAPA/IQAAAAAAAOA/GhsJAAAAAAAA8D8RAAAAAAAA8D8hAAAAAAAA4D8aGwkAAAAAAADwPxEAAAAAAADwPyEAAAAAAADgPxobCQAAAAAAAPA/EQAAAAAAAPA/IQAAAAAAAOA/GhsJAAAAAAAA8D8RAAAAAAAA8D8hAAAAAAAA4D8aGwkAAAAAAADwPxEAAAAAAADwPyEAAAAAAADgPxobCQAAAAAAAPA/EQAAAAAAAPA/IQAAAAAAAOA/GhsJAAAAAAAA8D8RAAAAAAAA8D8hAAAAAAAA4D8gAREAAAAAAADwPykAAAAAAADwPzEAAAAAAADwPzkAAAAAAADwP0LRARoSCQAAAAAAAOA/ETMzMzMzM+M/GhIJMzMzMzMz4z8RZmZmZmZm5j8aEglmZmZmZmbmPxGamZmZmZnpPxoSCZqZmZmZmek/Ec3MzMzMzOw/GhIJzczMzMzM7D8RAAAAAAAA8D8aGwkAAAAAAADwPxGamZmZmZnxPyEAAAAAAAAUQBoSCZqZmZmZmfE/ETQzMzMzM/M/GhIJNDMzMzMz8z8RzczMzMzM9D8aEgnNzMzMzMz0PxFmZmZmZmb2PxoSCWZmZmZmZvY/EQAAAAAAAPg/QqQCGhsJAAAAAAAA8D8RAAAAAAAA8D8hAAAAAAAA4D8aGwkAAAAAAADwPxEAAAAAAADwPyEAAAAAAADgPxobCQAAAAAAAPA/EQAAAAAAAPA/IQAAAAAAAOA/GhsJAAAAAAAA8D8RAAAAAAAA8D8hAAAAAAAA4D8aGwkAAAAAAADwPxEAAAAAAADwPyEAAAAAAADgPxobCQAAAAAAAPA/EQAAAAAAAPA/IQAAAAAAAOA/GhsJAAAAAAAA8D8RAAAAAAAA8D8hAAAAAAAA4D8aGwkAAAAAAADwPxEAAAAAAADwPyEAAAAAAADgPxobCQAAAAAAAPA/EQAAAAAAAPA/IQAAAAAAAOA/GhsJAAAAAAAA8D8RAAAAAAAA8D8hAAAAAAAA4D8gAQ==";
    </script>
  '>
</iframe>
