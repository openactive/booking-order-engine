<h1 class="c17" id="h.a51yq6t37gs6"><span class="c11">Order Engine Design</span></h1><p class="c1"><span class="c0">This document contains a high-level design of an Order Engine, which would ease the integration of Brokers with Booking Systems.</span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span class="c0">It has a dependency of a yet-to-be-built Feed Normaliser, and sits within an overall architecture of components that are outlined below. The Architecture and Feed Normaliser high-level designs are included here for context.</span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span class="c12">This work is licensed under a </span><span class="c3 c16"><a class="c7" href="https://www.google.com/url?q=http://creativecommons.org/licenses/by/4.0/&amp;sa=D&amp;ust=1580153836805000">Creative Commons Attribution 4.0 International License</a></span><span class="c6">.</span><span style="overflow: hidden; display: inline-block; margin: 0.00px 0.00px; border: 0.00px solid #000000; transform: rotate(0.00rad) translateZ(0px); -webkit-transform: rotate(0.00rad) translateZ(0px); width: 80.00px; height: 15.00px;"><img alt="" src="images/image4.png" style="width: 80.00px; height: 15.00px; margin-left: 0.00px; margin-top: 0.00px; transform: rotate(0.00rad) translateZ(0px); -webkit-transform: rotate(0.00rad) translateZ(0px);" title=""></span></p><h1 class="c17" id="h.9jqaud5oq8b5"><span class="c11">Architecture</span></h1><p class="c1"><span class="c0">The services are separated by their function, and after a thorough analysis there is little useful overlap between the three feed consumption services - such that a clean separation of concerns exists, as shown in the diagram below.</span></p><h2 class="c13" id="h.97betwfduslz"><span style="overflow: hidden; display: inline-block; margin: 0.00px 0.00px; border: 0.00px solid #000000; transform: rotate(0.00rad) translateZ(0px); -webkit-transform: rotate(0.00rad) translateZ(0px); width: 602.00px; height: 307.09px;"><img alt="" src="images/image1.png" style="width: 602.00px; height: 307.09px; margin-left: 0.00px; margin-top: 0.00px; transform: rotate(0.00rad) translateZ(0px); -webkit-transform: rotate(0.00rad) translateZ(0px);" title=""></span></h2><h1 class="c17" id="h.85r07lh74irl"><span class="c11">Production</span></h1><h2 class="c13" id="h.ltfakqqn0y1z"><span class="c14">Order Engine</span></h2><p class="c1"><span style="overflow: hidden; display: inline-block; margin: 0.00px 0.00px; border: 0.00px solid #000000; transform: rotate(0.00rad) translateZ(0px); -webkit-transform: rotate(0.00rad) translateZ(0px); width: 573.00px; height: 234.38px;"><img alt="" src="images/image6.png" style="width: 573.00px; height: 234.38px; margin-left: 0.00px; margin-top: 0.00px; transform: rotate(0.00rad) translateZ(0px); -webkit-transform: rotate(0.00rad) translateZ(0px);" title=""></span></p><h3 class="c10" id="h.pik1h1en4f5h"><span class="c5">Scope</span></h3><p class="c1"><span class="c0">Goals:</span></p><ul class="c8 lst-kix_8xfnpm7oac6i-0 start"><li class="c1 c2"><span class="c0">Microservice</span></li><li class="c1 c2"><span class="c0">Easy webhook configuration</span></li><li class="c1 c2"><span class="c0">Orchestrate Broker side Order logic, via hooks for Broker events in Booking flow</span></li><li class="c1 c2"><span class="c0">Handles rollbacks</span></li><li class="c1 c2"><span class="c0">Order persistence</span></li><li class="c1 c2"><span class="c0">Order feed processing and notifications</span></li><li class="c1 c2"><span class="c0">Passes through C1, C2, B</span></li><li class="c1 c2"><span class="c0">Provides webhooks for Payment Capture, Payment Auth, Payment Refund and Order updates (Customer notice, Opportunity attendance updates, and cancellations)</span></li><li class="c1 c2"><span class="c0">Uses Feed Normaliser for Change of Logistics notifications only</span></li><li class="c1 c2"><span class="c0">Persists client credentials and authentication tokens for each Seller, granted by the Booking System</span></li><li class="c1 c2"><span class="c0">Trigger and handle the associated OAuth flows</span></li><li class="c1 c2"><span class="c0">Generate basic HTML invoices</span></li></ul><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span class="c0">Non-goals:</span></p><ul class="c8 lst-kix_8xfnpm7oac6i-0"><li class="c1 c2"><span class="c0">Opportunity persistence</span></li><li class="c1 c2"><span class="c0">Aggregating opportunity data (though the Feed Normaliser can feed an Search service directly)</span></li><li class="c1 c2"><span class="c0">Use by Integration Tests</span></li></ul><h3 class="c10" id="h.xqnf8tpos9az"><span class="c5">Interfaces</span></h3><p class="c1"><span class="c0">Configured with:</span></p><ul class="c8 lst-kix_9t6symsqh4pd-0 start"><li class="c1 c2"><span class="c0">Feed Normaliser URL</span></li><li class="c1 c2"><span class="c0">OAuth Client credentials for each Booking System</span></li></ul><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span class="c0">Input:</span></p><ul class="c8 lst-kix_b2p0ixgnf4uv-0 start"><li class="c1 c2"><span class="c0">Dereferenced Data Catalogue from Feed Normaliser</span></li><li class="c1 c2"><span class="c0">Change of Logistics RPDE Feeds</span></li><li class="c1 c2"><span class="c0">Open Booking API endpoints</span></li><li class="c1 c2"><span class="c0">Get Opportunity by ID endpoint</span></li></ul><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span class="c0">Output</span></p><ul class="c8 lst-kix_7m1ve6jts2um-0 start"><li class="c1 c2"><span class="c0">C1, C2 and B passthrough</span></li><li class="c1 c2"><span class="c0">Payment Auth Webhook</span></li><li class="c1 c2"><span class="c0">Payment Auth Cancellation Webhook</span></li><li class="c1 c2"><span class="c0">Payment Capture Webhook</span></li><li class="c1 c2"><span class="c0">Payment Refund Webhook, and Cancellation Processed Webhook</span></li><li class="c1 c2"><span class="c0">Order Update Webhook</span></li><li class="c1 c2"><span class="c0">Change of Logistics Webhook</span></li><li class="c1 c2"><span class="c0">Customer Notification Webhook</span></li><li class="c1 c2"><span class="c0">Customer Notice Webhook</span></li><li class="c1 c2"><span class="c0">Opportunity attendance updates Webhook</span></li><li class="c1 c2"><span class="c0">Credentials management admin page</span></li></ul><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span style="overflow: hidden; display: inline-block; margin: 0.00px 0.00px; border: 0.00px solid #000000; transform: rotate(0.00rad) translateZ(0px); -webkit-transform: rotate(0.00rad) translateZ(0px); width: 601.50px; height: 466.61px;"><img alt="" src="images/image3.png" style="width: 601.50px; height: 466.61px; margin-left: 0.00px; margin-top: 0.00px; transform: rotate(0.00rad) translateZ(0px); -webkit-transform: rotate(0.00rad) translateZ(0px);" title=""></span></p><h3 class="c10" id="h.sy0krlvxkbj2"><span class="c5">Logistics Hash</span></h3><p class="c1"><span class="c0">A Change of Logistics feed endpoint should be available from the Feed Normaliser, which only includes opportunities where the contents of their logistics properties (e.g. due to change of address, startDate, etc) have changed. This can be achieved by computing a simple hash of the logistics properties, and comparing the old hash to the new hash on each RPDE item update. Where the hashes are different, a field is set against the item that includes it in the change of logistics feed at the time such a hash difference was noticed (this would likely be a &ldquo;logistics_updated&rdquo; field in Opportunity table, to ensure the change of logistics RPDE feed is ordered by logistics updates only. Null values of &ldquo;logistics_updated&rdquo; are simply not included in the feed). The logic to compute this hash should be included in the Harvester Utilities Library.</span></p><h3 class="c10" id="h.44m0kmsbwe8u"><span class="c5">Functional Specification</span></h3><p class="c1"><span class="c0">A microservice that is configured through environment variables (including webhook URLs).</span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span class="c0">The microservice acts as pass-through for C1, C2, and B calls, using the Get Opportunity by ID endpoint of the Feed Normaliser to check the validity of the opportunities requested, and that their logistics match those provided.</span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span>The microservice persists each Order that is created through B, along with the </span><span class="c3"><a class="c7" href="https://www.google.com/url?q=https://www.openactive.io/open-booking-api/EditorsDraft/%23booking-flow-rollback&amp;sa=D&amp;ust=1580153836811000">orderCreationStatus</a></span><span>&nbsp;defined in the Open Booking API specification. This allows it to effectively execute the rollback procedure. It also computes a </span><span class="c3"><a class="c7" href="#h.sy0krlvxkbj2">logistics hash</a></span><span class="c0">&nbsp;of each referenced Opportunity before storing it. The microservice relies on a Postgres database, to allow it to be easily run within a number of hosting environments.</span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span>The microservice processes the Change of Logistics feed from the Feed Normaliser, for each opportunity retrieved it computes the </span><span class="c3"><a class="c7" href="#h.sy0krlvxkbj2">logistics hash</a></span><span>&nbsp;against all stored Orders that reference the opportunity. It then runs a query to return all Orders that have a matching opportunity ID with a differing </span><span class="c3"><a class="c7" href="#h.sy0krlvxkbj2">logistics hash</a></span><span class="c0">, and triggers a Change of Logistics Webhook call for each, which highlights the properties that have changed - including the old and new content for each. </span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span class="c0">The microservice calls the Payment Auth and Payment Capture webhooks as part of processing B.</span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span class="c0">The microservice also processes the Orders feed, and triggers a webhook for any Order updates: Customer notice, Cancellation and Opportunity attendance updates. In the case of cancellation, it triggers a Payment Refund webhook.</span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span>The microservice will also handle the broker-side steps required to </span><span class="c3"><a class="c7" href="https://www.google.com/url?q=https://www.openactive.io/open-booking-api/EditorsDraft/%23step-by-step-process-description&amp;sa=D&amp;ust=1580153836813000">process B</a></span><span>, including retrying the Customer Notification webhook. It will also handle the </span><span class="c3"><a class="c7" href="https://www.google.com/url?q=https://www.openactive.io/open-booking-api/EditorsDraft/%23booking-flow-rollback&amp;sa=D&amp;ust=1580153836813000">booking flow rollback</a></span><span class="c0">, by calling the Payment Auth Cancellation, issuing an Order Deletion (DELETE) request (it does not attempt to retry payment).</span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span>The service generates basic HTML invoices associated with each Order (in accordance with the specification described </span><span class="c3"><a class="c7" href="https://www.google.com/url?q=https://www.gov.uk/guidance/vat-guide-notice-700%23information-required-on-a-vat-invoice&amp;sa=D&amp;ust=1580153836814000">here</a></span><span class="c0">), and makes them available at a URL which is included in the Customer Notification, Order Update, and Cancellation Processed Webhooks.</span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span>The service handles persisting the OAuth Client Credentials for each Booking System, and OAuth Access Tokens for each Seller within each Booking System. It also handles triggering the OAuth flows described </span><span class="c3"><a class="c7" href="https://www.google.com/url?q=https://tutorials.openactive.io/open-booking-sdk/quick-start-guide/storebookingengine/day-8-authentication&amp;sa=D&amp;ust=1580153836814000">here</a></span><span class="c0">.</span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span class="c0">A simple HTML page is available that allows the Broker to manage their client credentials for each booking system.</span></p><h3 class="c10" id="h.44ckkojzthw1"><span class="c5">Separation of Concerns</span></h3><p class="c1"><span class="c0">It is possible to split this microservice into an Orders Engine library and wrapping microservice, to provide more flexibility in implementation options. However given its dependency on a database for persistence and booking system authentication credentials, this document suggests that abstracting a library from the microservice is an opportunity for future iterations rather than for the initial build.</span></p><h3 class="c10" id="h.ba9xjkuc6kaq"><span class="c5">Suggested Task Breakdown</span></h3><ul class="c8 lst-kix_eb300q65awmz-0 start"><li class="c1 c2"><span class="c0">Create simple pass-through microservice for C1, C2 and B that stores Orders from B in a Postgres database. The jsonb data type should be used to allow the Orders to be queried.</span></li><li class="c1 c2"><span class="c0">Improve the B call to use the Get Opportunity by ID endpoint of the Feed Normaliser to check the validity of the opportunities requested, and that their logistics match those provided, and compute a logistics hash to store alongside each opportunity (in a new custom property) within the jsonb column. It should call the Customer Notification webhook when complete.</span></li><li class="c1 c2"><span class="c0">Add RPDE polling for the Orders feed from the booking system, and the Change of Logistics feed from the feed normaliser, using a similarly robust approach to that taken within the Feed Normaliser </span></li><li class="c1 c2"><span class="c0">Implement Change of Logistics logic in response to new Change of Logistics feed items, as specified, including Change of Logistics webhook.</span></li><li class="c1 c2"><span class="c0">Implement webhooks for the B happy path: Payment Auth, Payment Capture and Customer Notification</span></li><li class="c1 c2"><span class="c0">Implement cancellation logic in response to Order feed update: Payment Refund webhook followed by Cancellation Processed Webhook.</span></li><li class="c1 c2"><span class="c0">Implement other webhooks in response to Order feed update: Customer Notice Webhook, Opportunity attendance updates Webhook and generic Order Update Webhook - with the webhook triggered depending on the contents of the Order that has changed compared with the stored version. </span></li><li class="c1 c2"><span class="c0">Implement booking flow rollback as specified.</span></li><li class="c1 c2"><span class="c0">Generate HTML invoices as specified.</span></li><li class="c1 c2"><span class="c0">Persist a table of booking systems (identified by their dataset sites), for each they should have OAuth Client Credentials and OAuth Access Tokens for each Seller within them. The pass-through call to C1, C2 and B should pick up the correct tokens from this table and use them to make the call to the correct booking system.</span></li><li class="c1 c2"><span class="c0">Create a simple HTML page that lists the booking system&rsquo;s configured, the client credentials for each booking system, and how many Seller Access Tokens exist for that booking system. It allows client credentials for Booking Systems to be added to the database - the booking systems themselves are auto-populated to the page directly from the Dereferenced Single Data Catalog, but entries in the booking system table are made when Client Credentials are set against the booking system.</span></li><li class="c1 c2"><span class="c0">Create endpoint that triggers Seller OAuth flow</span></li></ul><p class="c1 c4"><span class="c0"></span></p><h2 class="c13" id="h.8zzj17y7qt8j"><span class="c14">Feed Normaliser</span></h2><p class="c1"><span class="c0">Consumes multiple feeds from each publisher&rsquo;s dataset and combines them into one</span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span style="overflow: hidden; display: inline-block; margin: 0.00px 0.00px; border: 0.00px solid #000000; transform: rotate(0.00rad) translateZ(0px); -webkit-transform: rotate(0.00rad) translateZ(0px); width: 573.00px; height: 303.26px;"><img alt="" src="images/image5.png" style="width: 573.00px; height: 303.26px; margin-left: 0.00px; margin-top: 0.00px; transform: rotate(0.00rad) translateZ(0px); -webkit-transform: rotate(0.00rad) translateZ(0px);" title=""></span></p><h3 class="c10" id="h.lap0j6ihacb3"><span class="c5">Scope</span></h3><p class="c1"><span class="c0">Goals:</span></p><ul class="c8 lst-kix_pxeilrtsah8j-0 start"><li class="c1 c2"><span class="c0">Data-driven configuration, so it will work &ldquo;out of the box&rdquo;</span></li><li class="c1 c2"><span class="c0">Read multiple dataset sites automatically from a data catalog</span></li><li class="c1 c2"><span class="c0">Combine all feeds into a normalised feed</span></li><li class="c1 c2"><span class="c0">Provide a change of logistics feed</span></li><li class="c1 c2"><span class="c0">Production-ready</span></li></ul><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span class="c0">Non-goals:</span></p><ul class="c8 lst-kix_w7loyzhp41nm-0 start"><li class="c1 c2"><span class="c0">Support for in-memory or test suite</span></li><li class="c1 c2"><span class="c0">Support for anything related to the Open Booking API</span></li></ul><h3 class="c10" id="h.v7ko0wrwatac"><span class="c5">Interfaces</span></h3><p class="c1"><span class="c0">Configured with:</span></p><ul class="c8 lst-kix_6hqt92egmr6v-0 start"><li class="c1 c2"><span class="c0">URL of JSON file containing data catalogue URLs</span></li></ul><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span class="c0">Input:</span></p><ul class="c8 lst-kix_1x8foka706k4-0 start"><li class="c1 c2"><span class="c0">Data Catalogues (which contain Dataset Site URLs)</span></li><li class="c1 c2"><span class="c0">Dataset Sites</span></li><li class="c1 c2"><span class="c0">Opportunity RPDE Feeds</span></li></ul><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span class="c0">Output</span></p><ul class="c8 lst-kix_wucoljsyulpo-0 start"><li class="c1 c2"><span class="c0">Single normalised feeds for Sessions, Facilities, Courses, etc</span></li><li class="c1 c2"><span>Single normalised feed for only Sessions, Facilities, Courses, etc that have had a </span><span>Change in Logistics</span><span class="c0">&nbsp;since the service started</span></li><li class="c1 c2"><span class="c0">Dereferenced single data catalog endpoint, that includes Dataset Site content for each of the feeds contained in the single normalised feed, and polling statistics/status for each</span></li><li class="c1 c2"><span>Get Opportunity by ID endpoint for all </span><span class="c3"><a class="c7" href="https://www.google.com/url?q=https://www.openactive.io/open-booking-api/EditorsDraft/%23definition-of-a-bookable-opportunity-and-offer-pair&amp;sa=D&amp;ust=1580153836819000">&ldquo;Bookable&rdquo; opportunity types</a></span></li></ul><h3 class="c10" id="h.rzzucqo3rkhi"><span class="c5">Functional Specification</span></h3><p class="c1"><span>The service should read from the Data Catalogs specified by the </span><span class="c3"><a class="c7" href="https://www.google.com/url?q=https://github.com/openactive/dataset-api-discovery/issues/8&amp;sa=D&amp;ust=1580153836819000">Data Catalog list</a></span><span class="c0">&nbsp;configured in an environment variable, and spider the referenced Dataset Sites, automatically registering feeds to consume. The list of Dataset Sites being consumed should be available via the Dereferenced single data catalog endpoint, with polling statistics/status for each.</span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span class="c0">This service normalises ingested feeds from these into a standard data model (ScheduledSession, SessionSeries, EventSeries, etc), contained within tables &ldquo;Opportunity&rdquo;, &ldquo;ParentOpportunity&rdquo;, and &ldquo;GrandparentOpportunity&rdquo; (which include the item JSON), and provide a single feed as output that is created by joining these three tables. Note that when a &ldquo;ParentOpportunity&rdquo; entry is updated, all &ldquo;Opportunity&rdquo; entries must be updated to ensure they are included in the normalised feed.</span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span>The feeds within each dataset site should be automatically recognised - using the &ldquo;parent&rdquo; attribute of </span><span class="c3"><a class="c7" href="https://www.google.com/url?q=https://github.com/openactive/data-models/blob/master/versions/2.x/meta.json%23L9&amp;sa=D&amp;ust=1580153836820000">meta.json</a></span><span class="c0">&nbsp;to automatically infer &ldquo;Opportunity&rdquo; and &ldquo;OpportunityParent&rdquo;.</span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span class="c0">There is much overlap of the logic used to normalise feeds in-memory within the Feed Listener, and such logic should be moved into the Harvester Utilities Library to be shared between both codebases.</span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span>Given this service is designed for production, the RPDE polling mechanism needs to be robust e.g. using </span><span class="c3"><a class="c7" href="https://www.google.com/url?q=https://www.npmjs.com/package/async-polling&amp;sa=D&amp;ust=1580153836821000">https://www.npmjs.com/package/async-polling</a></span><span class="c0">. Additionally it needs to be able to recover from a service outage, and hence the &lsquo;next&rsquo; URL for each feed must be persisted to allow for such recovery.</span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span class="c0">There should be a configuration option to not retain or propagate events in the past (i.e they don&rsquo;t get stored, and are filtered out at the point of ingestion), to minimise the size of the persistence required.</span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span>For each item, optionally (via configuration) run </span><span class="c3"><a class="c7" href="https://www.google.com/url?q=https://github.com/openactive/data-model-validator/&amp;sa=D&amp;ust=1580153836822000">data-model-validator</a></span><span class="c0">&nbsp;validation and store results - this validation is carried out on the composited opportunity rather than on individual components. Results are included in the normalised feed as &ldquo;errors&rdquo; alongside the RPDE &ldquo;data&rdquo; property. </span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span class="c0">A Change of Logistics feed endpoint is also available, which only includes opportunities where the contents of their logistics properties (e.g. due to change of address, startDate, etc) have changed. This can be achieved by computing a simple hash of the logistics properties, and comparing the old hash to the new hash on each RPDE item update. Where the hashes are different, a field is set against the item that includes it in the change of logistics feed at the time such a hash difference was noticed (this would likely be a &ldquo;logistics_updated&rdquo; field in Opportunity table, to ensure the change of logistics RPDE feed is ordered by logistics updates only. Null values of &ldquo;logistics_updated&rdquo; are simply not included in the feed). The logic to compute this hash should be included in the Harvester Utilities Library.</span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span class="c0">There is a mechanism, via a simple API endpoint, to reset all feeds within a specific dataset - causing its normalised content to be purged from the database, and for the feeds within the dataset to be synchronised again.</span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span>The Get Opportunity ID endpoint that returns the full composited JSON-LD for any of the </span><span class="c3"><a class="c7" href="https://www.google.com/url?q=https://www.openactive.io/open-booking-api/EditorsDraft/%23definition-of-a-bookable-opportunity-and-offer-pair&amp;sa=D&amp;ust=1580153836823000">&ldquo;Bookable&rdquo; opportunity types</a></span><span class="c0">, including their embedded parents, as per the diagram below:</span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span style="overflow: hidden; display: inline-block; margin: 0.00px 0.00px; border: 0.00px solid #000000; transform: rotate(0.00rad) translateZ(0px); -webkit-transform: rotate(0.00rad) translateZ(0px); width: 602.00px; height: 209.23px;"><img alt="" src="images/image2.png" style="width: 602.00px; height: 209.23px; margin-left: 0.00px; margin-top: 0.00px; transform: rotate(0.00rad) translateZ(0px); -webkit-transform: rotate(0.00rad) translateZ(0px);" title=""></span></p><p class="c1 c4"><span class="c0"></span></p><p class="c1"><span>An API endpoint should be available that includes a dereferenced single data catalog, which contains Dataset Site content for each of the feeds contained in the single normalised feed, and polling statistics/status for each too. This can be achieved by augmenting the DataDownload objects in the distribution array within the </span><span class="c9">Dataset</span><span class="c0">&nbsp;with custom namespaced properties as follows:</span></p><ul class="c8 lst-kix_gfleg554yf8e-0 start"><li class="c1 c2"><span>Number of pages processed successfully: &ldquo;</span><span class="c9">feedstatus:pagesProcessedSuccessfully</span><span class="c0">&rdquo;</span></li><li class="c1 c2"><span>Number of feed read errors encountered: &ldquo;</span><span class="c9">feedstatus:pagesProcessedWithErrors</span><span class="c0">&rdquo;</span></li><li class="c1 c2"><span>Last processed page timestamp: &ldquo;</span><span class="c9">feedstatus:lastPageReceivedDate</span><span class="c0">&rdquo;,</span></li><li class="c1 c2"><span>Number of updates received successfully: &ldquo;</span><span class="c9">feedstatus:itemsProcessedSuccessfully</span><span class="c0">&rdquo;</span></li><li class="c1 c2"><span>Number of validator errors for these updates: &ldquo;</span><span class="c9">feedstatus:itemsProcessedWithErrors</span><span class="c0">&rdquo;</span></li></ul><h3 class="c10" id="h.bkutb3tlyuh3"><span class="c5">Suggested Task Breakdown</span></h3><ul class="c8 lst-kix_wu6uhp5dqyen-0 start"><li class="c1 c2"><span class="c0">Using Feed Listener as inspiration, create a Postgres-based version of the single normalised feed, using a single dataset site in order to receive URL configuration of feeds.</span></li><li class="c1 c2"><span>Extend the implementation to work with multiple Dataset Sites, spidering data catalogs by ingesting a </span><span class="c3"><a class="c7" href="https://www.google.com/url?q=https://github.com/openactive/dataset-api-discovery/issues/8&amp;sa=D&amp;ust=1580153836826000">Data Catalog List</a></span><span class="c0">&nbsp;as specified by an environment variable, and updating this hourly.</span></li><li class="c1 c2"><span class="c0">Implement dereferenced single data catalog endpoint, including polling statistics/status for each feed as well as the original Dataset Site JSON-LD content.</span></li><li class="c1 c2"><span class="c0">Implement reliable RPDE polling mechanism, including rate limiting (using a global rate limit) and exponential backoff (with a limit) in the case of error. RPDE page being ingested should be persisted to ensure the service is robust resume after an outage.</span></li><li class="c1 c2"><span class="c0">Implement configurable feature to prevent events in the past from being ingested and stored.</span></li><li class="c1 c2"><span class="c0">Implement Get Opportunity by ID endpoint</span></li><li class="c1 c2"><span>Run </span><span class="c3"><a class="c7" href="https://www.google.com/url?q=https://github.com/openactive/data-model-validator/&amp;sa=D&amp;ust=1580153836828000">data-model-validator</a></span><span class="c0">&nbsp;against each composite opportunity: this validation is carried out on the composited opportunity rather than on components from individual feeds. This can be done at the point of writing to the &ldquo;Opportunity&rdquo; table, where a &ldquo;ParentOpportunity&rdquo; exists.</span></li><li class="c1 c2"><span class="c0">Implement dataset reset mechanism</span></li><li class="c1 c2"><span class="c0">Implement change of logistics notification feed</span></li></ul>
