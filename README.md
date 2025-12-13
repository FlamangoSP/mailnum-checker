The validate_emails.py is a Python-based tool for email verification and email validation that allows you to classify your email addresses' status using syntax, DNS, and SMTP (Simple Mail Transfer Protocol) and other checks and 3rd party APIs. Using the script's returned status classifications, you can then clean or scrub your email lists. This can be done self-hosted locally on a server or via the supported commercial email verification service APIs for EmailListVerify, MillionVerifier, MyEmailVerifier, CaptainVerify, Proofy.io, Zerobounce, Reoon, Bouncify, Bounceless (jump straight to email verification provider API speed and result benchmarks). The validate_emails.py script is coded to conditionally support each commercial email verification providers' API rate limits and can be tuned via argument -wf work factor to adjust the number of concurrent threads used for single email address API requests. This ensures you do not waste time and credit $$$$ running into API rate limits.

The script's API Merge support also allows you to combine 2 API email verification providers results into one JSON formated output for double verification checks. The script provides a convenient way to verify the existence and deliverability of email addresses, helping you maintain a clean and accurate email list.

The script offers specific support for Xenforo forum member email list verification through dedicated Xenforo argument flags. These flags enable you to mark invalid Xenforo forum member emails and move them to a bounce_email status, effectively disabling Xenforo email sending to those members without actually deleting the Xenforo member account. You can then setup a Xenforo forum wide notice targetting bounce_email status users - prompting them to update their email addresses.

To reduce potential 3rd party email verification API costs, this script also supports Cloudflare HTTP Forward Proxy Cache With KV Storage for both per email check and bulk file API check results to be temporarily cached and return the email verification status codes at the Cloudflare CDN and Cloudflare Worker KV storage level.

Email verification results can also be optionally saved to Amazon AWS S3 and Cloudflare R2 object storage for long term storage and retrieval.

You can check out Email Verification Provider Comparison Costs table to visually compare their costs or jump straight to email verification provider API speed and result benchmarks.

The validate_emails.py email validation script was written by George Liu (eva2000) for his paid consulting clients usage. The below is public documentation for the script.

Features
Validates email addresses using syntax, DNS and SMTP checks
Validates -f from email address's SPF, DKIM, DMARC records and logs them for troubleshooting mail deliverability
Optionally save your email verification results to S3 object storage providers - Cloudflare R2 or Amazon S3
Support local self-hosted email verification + API support for:
EmailListVerify [example, bulk API]
MillionVerifier [example, bulk API]
MyEmailVerifier [example]
CaptainVerify [example]
Proofy.io [example]
Zerobounce [example]
Reoon [example]
Bouncify [example]
Bounceless [example]
API Merge support via -apimerge argument to merge EmailListVerify + MillionVerifier API results together for more accurate email verification results.
Supports Cloudflare HTTP Forward Proxy Cache With KV Storage for EmailListVerify per email check API
Classifies email addresses into various categories based on the syntax, DNS, and SMTP response
Supports concurrent processing for faster validation of multiple email addresses
Provides detailed logging for tracking the validation process
Allows customization of delay between requests to respect email server limitations
Supports input of email addresses via command-line arguments or a file
Identifies disposable email addresses and free domain name provider addresses
Checks email addresses against custom blacklists and whitelists
Supports different test modes for syntax, DNS, SMTP, and disposable email checks
Configurable SMTP port and TLS/SSL support
Supports SMTP profiles. However, using SMTP relay profiles won't get accurate SMTP checks. Locally ran server SMTP checks are more accurate.
Supports different DNS lookup methods: asyncio, concurrent, and sequential
Supports different processing modes: thread and asyncio
Xenforo support. Generates SQL queries for updating user status user_state in XenForo forum based email validation results. Allowing you to clean up your Xenforo user database's email addresses by disabling email sending to those specific bad email addresses.
Requirements
Python 3.6 minimum. Script tested on AlmaLinux 8 Python 3.6 and AlmaLinux 9 Python 3.9.
Usage
Open a terminal or command prompt and navigate to the directory where the script is located.

Run the script with the desired command-line arguments.

python validate_emails.py
usage: validate_emails.py [-h] -f FROM_EMAIL [-e EMAILS] [-l LIST_FILE] [-b BATCH_SIZE] [-d] [-v] [-delay DELAY] [--cache-timeout CACHE_TIMEOUT]
                          [-t TIMEOUT] [-r RETRIES] [-tm {syntax,dns,smtp,all,disposable}] [-dns {asyncio,concurrent,sequential}]
                          [-p {thread,asyncio}] [-bl BLACKLIST_FILE] [-wl WHITELIST_FILE] [-smtp {default,ses,generic,rotate}] [-xf]
                          [-xfdb XF_DATABASE] [-xfprefix XF_PREFIX] [-profile] [-wf WORKER_FACTOR]
                          [-api {emaillistverify,millionverifier,captainverify,proofy,myemailverifier,zerobounce,reoon,bouncify,bounceless}]
                          [-apikey EMAILLISTVERIFY_API_KEY] [-apikey_mv MILLIONVERIFIER_API_KEY]
                          [-apibulk {emaillistverify,millionverifier,proofy,bounceless}] [-apikey_cv CAPTAINVERIFY_API_KEY]
                          [-apikey_pf PROOFY_API_KEY] [-apiuser_pf PROOFY_USER_ID] [-pf_max_connections PROOFY_MAX_CONNECTIONS]
                          [-pf_batchsize PROOFY_BATCH_SIZE] [-apikey_mev MYEMAILVERIFIER_API_KEY] [-apikey_zb ZEROBOUNCE_API_KEY]
                          [-apikey_rn REOON_API_KEY] [-reoon_mode {quick,power}] [-reoon_max_connections REOON_MAX_CONNECTIONS]
                          [-apikey_bf BOUNCIFY_API_KEY] [-apikey_bl BOUNCELESS_API_KEY] [-mev_max_connections MEV_MAX_CONNECTIONS] [-apimerge]
                          [-apicache {emaillistverify,zerobounce,millionverifier}] [-apicachettl APICACHETTL]
                          [-apicachecheck {count,list,purge}] [-apicache-purge] [-store {r2,s3}] [-store-list]
validate_emails.py: error: the following arguments are required: -f/--from_email
The available arguments are:

-f, --from_email (required):
Description: The email address to use in the MAIL FROM command.
-e, --emails (optional):
Description: A single email or comma-separated list of emails to check.
-l, --list_file (optional):
Description: The path to a file containing emails, one per line.
-b, --batch_size (optional):
Description: The number of concurrent processes to use (default is 1).
-d, --debug (optional):
Description: Enable debug logging for more verbose output.
-v, --verbose (optional):
Description: Enable verbose output.
-delay, --delay (optional):
Description: The delay between requests in seconds (default is 1).
--cache-timeout (optional):
Description: Set the caching resolver timeout value (default is 14400).
-t, --timeout (optional):
Description: The timeout in seconds for SMTP connection and commands (default is 10).
-r, --retries (optional):
Description: The number of retries for temporary failures and timeouts (default is 3).
-tm, --test-mode (optional):
Description: The test mode to use for email validation. Available options are:
syntax: Check email format syntax only.
dns: Perform DNS validation only (default).
smtp: Perform SMTP validation only.
all: Perform all validations.
disposable: Check if the email is from a disposable domain.
-dns, --dns-method (optional):
Description: The DNS lookup method to use. Available options are:
asyncio: Use asynchronous DNS lookups using the asyncio library (default).
concurrent: Use concurrent DNS lookups using the concurrent.futures library.
sequential: Use basic sequential DNS lookups.
-p, --process_mode (optional):
Description: The processing mode to use for the process_emails function. Available options are:
thread: Use thread-based processing (default).
asyncio: Use asyncio-based processing.
-bl, --blacklist_file (optional):
Description: The path to a file containing blacklisted domains, one per line.
-wl, --whitelist_file (optional):
Description: The path to a file containing whitelisted domains, one per line.
-smtp, --smtp_profile (optional):
Description: The SMTP profile to use for email validation. Available options are:
default: Use the default SMTP settings (default).
ses: Use Amazon SES SMTP settings via ses.ini config file.
generic: Use generic SMTP settings via smtp.ini config file.
rotate: Use multiple SMTP profile settings via rotate.ini config file which you can rotate through.
-xf, --xf_sql (optional):
Description: Generate SQL queries for updating user_state in XenForo for emails with specific statuses.
-xfdb, --xf_database (optional):
Description: The XenForo database name (default is 'DATABNAME').
-xfprefix, --xf_prefix (optional):
Description: The XenForo table prefix (default is 'xf_').
-profile, --profile (optional):
Description: Enable profiling of the script.
-wf, --worker-factor (optional):
Description: The worker factor for calculating the maximum number of worker threads (default is 16).
-api, --api (optional):
Description: Specify the API to use for email verification. Available options are:
emaillistverify: Use the EmailListVerify API.
millionverifier: Use the MillionVerifier API.
myemailverifier: Use the MyEmailVerifier API.
captainverify: Use the CaptainVerify API.
proofy: Use the Proofy API.
zerobounce: Use the Zerobounce.net API.
reoon: Use the Zerobounce.net API.
bouncify: Use the Bouncify API.
bounceless: Use the Bounceless API.
-apimerge, --api_merge (optional):
Description: Merge and combine emaillistverify or millionverifier API results into one result
-apibulk, --api_bulk (optional):
Description: Use emaillistverify or millionverifier values for Bulk file API method.
-apikey, --emaillistverify_api_key (optional):
Description: The API key for the EmailListVerify service.
-apikey_mv, --millionverifier_api_key (optional):
Description: The API key for the MillionVerifier service.
-apikey_mev, --myemailverifier-api-key (optional):
Description: The API key for the MyEmailVerifier service.
-apikey_cv, --captainverify_api_key (optional):
Description: The API key for the CaptainVerify service.
-apikey_zb, --zerobounce_api_key (optional):
Description: The API key for the Zerobounce.net service.
-apikey_rn, --reoon_api_key (optional):
Description: The API key for the Reoon service.
-reoon_mode, --reoon_mode
Description: Reoon verification mode quick or power
-apikey_bf, --bouncify_api_key
Description: The API key for the Bouncify service.
-apikey_bl, --bounceless_api_key
Description: The API key for the Bounceless service.
-apikey_pf, --proofy_api_key (optional):
Description: The API key for the Proofy service.
-apiuser_pf, --proofy_user_id (optional):
Description: The Proofy userid.
-pf_max_connections (optional):
Description: Maximum number of concurrent connections for the Proofy.io API (default: 1)
-mev_max_connections (optional):
Description: Maximum number of concurrent connections for the MyEmailVerifier API (default: 1)
-reoon_max_connections (optional):
Description: Maximum number of concurrent connections for the Reoon API (default: 5)
-apicache, --api_cache (optional):
Description: Set the appropriate API's Cloudflare Worker KV cacheKey prefix. Available options are:
emaillistverify: Use with the EmailListVerify API.
zerobounce: Use with the ZeroBounce API.
millionverifier: Use with the MillionVerifier API.
-apicachettl (optional):
Description: this sets the cache TTL duration in seconds for how long Cloudflare CDN/KV stores in cache (default: 300 seconds)
-apicachecheck (optional):
Description: operates when -apicachettl is set and takes count or list or purge options to query the Cloudflare KV storage cache to count number of cached entries or list the entries themselves
-apicache-purge (optional):
Description: purges Cloudflare CDN/KV cache when -apicachecheck set to purge option
Validates -f from email address's SPF, DKIM, DMARC records when argument is passed and logs them

python validate_emails.py -f user@domain1.com
cat email_verification_log_2024-05-05_01-54-51.log

2024-05-05 01:54:51,105 - INFO - SPF record found for user@domain1.com
2024-05-05 01:54:51,105 - INFO - SPF record: "v=spf1 include:_spf.google.com +a +mx ~all"
2024-05-05 01:54:51,142 - ERROR - Error checking DKIM for user@domain1.com with selector default: The DNS response does not contain an answer to the question: default._domainkey.domain1.com. IN TXT
2024-05-05 01:54:51,174 - INFO - DKIM record found for user@domain1.com with selector google
2024-05-05 01:54:51,174 - INFO - DKIM record: "v=DKIM1; k=rsa; p=MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCMF3nm2Za6EN0udFQLb35Jcy3u63iCzdaojAkVsCISJsHKe2ThgSsriU1MRm32abcd/u2aNEAxJ3jhN7TbkuV8j7xppV5PW+abcd/84lxa2xTlngXOymlJWleoTZUQQPkmkB66IO/XqZVj7RrF/Iru1qpAvJ9aW+6vCZEJFjCZowIDAQAB"
2024-05-05 01:54:51,237 - WARNING - Error checking DMARC for user@domain1.com with record name _dmarc: The DMARC record must be located at _dmarc._dmarc.domain1.com, not _dmarc.domain1.com
2024-05-05 01:54:51,562 - INFO - DMARC record found for user@domain1.com with record name _dmarc.mail
2024-05-05 01:54:51,562 - INFO - DMARC record: v=DMARC1; p=quarantine; sp=none; rua=mailto:re+xxx@inbound.dmarcdigests.com,mailto:re+yyy@dmarc.postmarkapp.com; aspf=r; pct=100
2024-05-05 01:54:51,562 - INFO - DMARC policy check passed for user@domain1.com
Example usage for DNS only checks (skipping SMTP checks):

python validate_email.py -f sender@example.com -e user1@example.com,user2@example.com -tm dns
Example usage for DNS + SMTP checks:

python validate_email.py -f sender@example.com -e user1@example.com,user2@example.com -tm all
Notes:

If the -tm flag is not passed, it defaults to DNS test mode only.
Tuning -wf worker factor value for calculating the maximum number of worker threads can speed up the processing of emails when in -p thread process mode or when no -p flag is set. Example benchmark of 10,000 email addresses for -tm dns -p thread for DNS only tests (using default -dns asyncio) using process method = thread took 4mins 40s to complete with work factor -wf 4. However, with -wf 80 time to completion took ~40s on a Intel Core i7 4790K 4C/8T or ~33s on a Intel Xeon E-2276G 6C/12T based dedicated server.
Output
The script outputs the validation results in JSON format. Each email address is represented by an object with the following fields:

email: The email address.
status: The validation status of the email address. Possible values are:
valid_format: The email address has a valid format.
invalid: The email address has an invalid format.
valid_dns: The email address has valid DNS records.
invalid_dns: The email address has invalid DNS records.
ok: The email address passed SMTP validation.
unknown_email: The email address is unknown or doesn't exist.
temporary_failure: A temporary failure occurred during SMTP validation.
syntax_or_command_error: An SMTP syntax or command error occurred.
transaction_failed: The SMTP transaction failed.
timeout: A timeout occurred during SMTP validation.
skipped_smtp_check: SMTP validation was skipped based on the test mode.
status_code: SMTP validation check's logged SMTP response code.
free_email: Indicates whether the email address is from a free email provider. Possible values are yes, no, or unknown.
disposable_email: Indicates whether the email address is from a disposable domain. Possible values are yes, no, or notchecked.
xf_sql (optional): The SQL query for updating the user status in XenForo based on the validation result.
