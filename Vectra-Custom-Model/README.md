A Custom Model can be deployed in Vectra by uploading [NanoCore_command-and-Control_Heartbeat.json](https://raw.githubusercontent.com/Abjuri5t/Hunting-NanoCore/main/Vectra-Custom-Model/NanoCore_Command-and-Control_Heartbeat.json) to Recall and intiating it in Detect as a custom model. Documentation on deploying custom models can be found in the [Creating custom Detections using Recall Metadata](https://support.vectra.ai/s/article/KB-VS-1179) support article.

The query `((local_orig:true AND local_resp:false) OR (local_orig:false AND local_resp:true)) AND protoName:"TCP" AND service:"unknown (unknown)" AND first_orig_resp_data_pkt:(QAAAA* OR OAAAAA*) AND first_resp_orig_data_pkt:IAAAA*` can be used to alert on potential NanoCore command-and-control connections. In practice, the detection may be triggered multiple times in a row as NanoCore's C2s have a tendency to drop and reset connections. I only discovered one false-positive in testing, which was triggered by online gaming activity related to Google.

There are three parts to the Recall query:
- `first_orig_resp_data_pkt:(QAAAA* OR OAAAA*) AND first_resp_orig_data_pkt:IAAAA*` observe the contents of the RAT's introduction message and the C2 server's response
- `protoName:"TCP" AND service:"unknown (unknown)"` functions as a secondary condition to speed-up searches and further mitigate false positives
- `(local_orig:true AND local_resp:false) OR (local_orig:false AND local_resp:true)` ensures that the observered traffic is only North-South - not East-West.
