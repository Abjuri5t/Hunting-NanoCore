A Custom Model can be deployed in Vectra by making use of the Recall search in [query.txt](https://github.com/Abjuri5t/Hunting-NanoCore/blob/main/Vectra-Custom-Model/query.txt). Documentation on deploying custom models can be found in the [Creating custom Detections using Recall Metadata](https://support.vectra.ai/s/article/KB-VS-1179) support article.

The query `((local_orig:true AND local_resp:false) OR (local_orig:false AND local_resp:true)) AND protoName:"TCP" AND service:"unknown (unknown)" AND first_orig_resp_data_pkt:(QAAAA* OR OAAAAA*) AND first_resp_orig_data_pkt:IAAAA*` can be used to alert on potential NanoCore command-and-control connections. In practice, the detection may be triggered multiple times in a row as NanoCore's C2s have a tendency to drop and reset connections. I was unable to find any false positives in my testing - but please contact me if a false positive does occur so that I can investigate and improve the query

There are three parts to the Recall query:
- `first_orig_resp_data_pkt:(QAAAA* OR OAAAA*) AND first_resp_orig_data_pkt:IAAAA*` observe the contents of the RAT's introduction message and the C2 server's response
- `protoName:"TCP" AND service:"unknown (unknown)"` functions as a secondary condition to speed-up searches and further mitigate false positives
- `(local_orig:true AND local_resp:false) OR (local_orig:false AND local_resp:true)` ensures that the observered traffic is only North-South - not East-West.
