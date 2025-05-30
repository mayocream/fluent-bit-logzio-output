# Logz.io-Out Plugin for Fluent Bit

You have two options when running Logz.io-Out Plugin for Fluent Bit:

* [Run as a standalone app](#standalone-config)
* [Run in a Docker container](#docker-config)

<div id="standalone-config">

## Run as a standalone app

### Configuration

#### 1.  Install Fluent Bit

If you haven't installed Fluent Bit yet,
you can build it from source
according to the [instructions from Fluent Bit](https://docs.fluentbit.io/manual/installation/getting-started-with-fluent-bit).

#### 2.  Install and configure the Logz.io plugin

First, find the **plugin directory** for your Fluent Bit installation (e.g., `/usr/lib/fluent-bit/plugins/`, `C:\Program Files\fluent-bit\lib\`, or a custom path). You will need to move the downloaded file into this directory. Check your Fluent Bit documentation or setup if unsure.

The commands below will download the **latest** stable version of the plugin binary for your platform into your current working directory.

- *(Optional)* If you need a specific version instead of the latest, replace `latest` in the download URL with the desired version tag (e.g., `v0.6.3`). You can find tags on the [Releases page](https://github.com/logzio/fluent-bit-logzio-output/releases).


* **For Linux (amd64):**
    ```shell
    wget -O /fluent-bit/plugins/out_logzio.so \
    https://github.com/logzio/fluent-bit-logzio-output/releases/latest/download/out_logzio-linux-amd64.so
    ```

* **For Linux (arm64):**
    ```shell
    wget -O /fluent-bit/plugins/out_logzio.so \
    https://github.com/logzio/fluent-bit-logzio-output/releases/latest/download/out_logzio-linux-arm64.so
    ```

* **For macOS (amd64):**
    ```shell
    wget -O /fluent-bit/plugins/out_logzio.so \
    https://github.com/logzio/fluent-bit-logzio-output/releases/latest/download/out_logzio-macos-amd64.so
    ```

* **For macOS (arm64):**
    ```shell
    wget -O /fluent-bit/plugins/out_logzio.so \
    https://github.com/logzio/fluent-bit-logzio-output/releases/latest/download/out_logzio-macos-arm64.so
    ```

* **For Windows (amd64):**
    ```powershell
    $pluginDir = "C:\fluent-bit\plugins" # Example path - CHANGE IF YOURS IS DIFFERENT
    $fileName = "out_logzio-windows-amd64.dll"
    $downloadUrl = "https://github.com/logzio/fluent-bit-logzio-output/releases/latest/download/{0}" -f $fileName
    $outputFile = Join-Path $pluginDir "out_logzio.dll"
    
    Invoke-WebRequest -Uri $downloadUrl -OutFile $outputFile 
    ```

Finally, in your Fluent Bit configuration file (`fluent-bit.conf` by default), add Logz.io as an output. Ensure Fluent Bit is configured to load plugins from the directory where you saved the file (this might be automatic, or require the `Plugins_File` directive or the `-e` startup flag pointing to the specific `.so`/`.dll` file).

**Note**:
Logz.io-Out Plugin for Fluent Bit
supports one output stream to Logz.io.
We plan to add support for multiple streams in the future. <br>
In the meantime,
we recommend running a new instance for each output stream you need.

For a list of options, see the [configuration parameters](#config-params) below to add to the code block. 👇

```python
[OUTPUT]
    Name  logzio
    Match *
    Workers 1
    logzio_token <<SHIPPING-TOKEN>>
    logzio_url   https://<<LISTENER-HOST>>:8071
    id <<YOUR-OUTPUT-ID>>
    logzio_type <<LOG-TYPE>>
    logzio_bulk_size_mb 2
```
#### 3.  Run Fluent Bit with the Logz.io plugin

The commands below assume you have downloaded the Logz.io plugin to a standard path and your Fluent Bit configuration is also in a standard location. If your paths differ, you will need to adjust them.

* **Expected Plugin Paths:**
    * Linux/macOS: `/fluent-bit/plugins/out_logzio.so`
    * Windows: `C:\fluent-bit\plugin\out_logzio.dll`
* **Expected Config Paths:**
    * Linux/macOS: `/fluent-bit/etc/fluent-bit.conf`
    * Windows: `C:\fluent-bit\etc\fluent-bit.conf`

**Example Run Commands:**

* **For Linux/macOS:**
    ```shell
    fluent-bit -e /fluent-bit/plugins/out_logzio.so -c /fluent-bit/etc/fluent-bit.conf
    ```

* **For Windows (Command Prompt):**
    ```shell
    fluent-bit.exe -e C:\fluent-bit\plugin\out_logzio.dll -c C:\fluent-bit\etc\fluent-bit.conf
    ```

**Note:** Ensure your `fluent-bit.conf` includes the `[OUTPUT]` section for Logz.io as described earlier. The `-e` flag explicitly loads the plugin from the specified path.

#### 4.  Check Logz.io for your logs

Give your logs some time to get from your system to ours, and then open [Kibana](https://app.logz.io/#/dashboard/kibana).

If you still don't see your logs, see [log shipping troubleshooting](https://docs.logz.io/user-guide/log-shipping/log-shipping-troubleshooting.html).

</div>

<div id="docker-config">

## Run in a Docker container

This refers to running the pre-built logzio/fluent-bit-output Docker image which already contains the plugin and its dependencies.

### Configuration

#### 1.  Make the configuration file

To run in a container,
create a configuration file named `fluent-bit.conf`.

**Note**:
Logz.io-Out Plugin for Fluent Bit
supports one output stream to Logz.io.
We plan to add support for multiple streams in the future. <br>
In the meantime,
we recommend running a new instance for each output stream you need.

For a list of options, see the [configuration parameters](#config-params) below to add to the code block. 👇

```python
[SERVICE]
    # Include your remaining SERVICE configuration here.
    Plugins_File plugins.conf

[OUTPUT]
    Name  logzio
    Match *
    logzio_token <<SHIPPING-TOKEN>>
    logzio_url   https://<<LISTENER-HOST>>:8071
    id <<YOUR-OUTPUT-ID>>
    logzio_type <<LOG-TYPE>>
```
#### 2.  Run the Docker image

Run the Docker image, mounting your configuration file into the container. Remember to replace <TAG> with the desired image tag

```shell
# Make sure your fluent-bit.conf is in the current directory or provide full path
docker run --rm -v $(pwd)/fluent-bit.conf:/fluent-bit/etc/fluent-bit.conf \
logzio/fluent-bit-output:<TAG>
```

#### 3.  Check Logz.io for your logs

Give your logs some time to get from your system to ours, and then open [Kibana](https://app.logz.io/#/dashboard/kibana).

If you still don't see your logs, see [log shipping troubleshooting](https://docs.logz.io/user-guide/log-shipping/log-shipping-troubleshooting.html).

</div>

<div id="config-params">

## Output Parameters

| Parameter           | Description                                                                                                                                                                                                                                                                                                     |
|---------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logzio_token        | **Required**. Replace `<<SHIPPING-TOKEN>>` with the [token](https://app.logz.io/#/dashboard/settings/general) of the account you want to ship to.                                                                                                                                                               |
| logzio_url          | **Default**: `https://listener.logz.io:8071`  Listener URL and port. Replace `<<LISTENER-HOST>>` with your region's listener host (for example, `listener.logz.io`). For more information on finding your account's region, see [Account region](https://docs.logz.io/user-guide/accounts/account-region.html). |
| logzio_type         | **Default**: `logzio-fluent-bit`  The [log type](https://docs.logz.io/user-guide/log-shipping/built-in-log-types.html), shipped as `type` field. Used by Logz.io for consistent parsing. Can't contain spaces.                                                                                                  |
| logzio_bulk_size_mb  | **Default**: `2` Max uncompressed bulk size (MB) before flushing (1-9). Lower values prevent crashes/reduce memory; higher values may increase throughput but use more resources. |
| logzio_debug        | **Default**: `false`  Set to `true` to print debug messages to stdout.                                                                                                                                                                                                                                          |
| id                  | **Required**. Replace `<<YOUR-OUTPUT-ID>>` with your output ID. e.g: `logzio_output_1` . Recommended to set explicitly.                                                                                                                                                                                                                               |
| dedot_enabled       | **Default**: `false`  Enabled dedot processing.                                                                                                                                                                                                                                                                 |
| dedot_nested        | **Default**: `false`  Enables nesting dedot processing.                                                                                                                                                                                                                                                         |
| dedot_new_separator | **Default**: `"_"`  Separator character to use when applying dedot processing.                                                                                                                                                                                                                                  |
| proxy_host          | **Optional**: `<PROXY_HOST>:<PROXY_PORT>`  Support HTTP proxy processing.                                                                                                                                                                                                                                       |
| proxy_user          | **Optional**: `""`  Support HTTP proxy user authentication.                                                                                                                                                                                                                                                     |
| proxy_pass          | **Optional**: `""`  Support HTTP proxy password authentication.                                                                                                                                                                                                                                                 |
| headers             | **Optional**: Custom HTTP headers in the format Key1:Value1,Key2:Value2. Duplicate keys will overwrite existing values.                                                                                                                                                                                         |
</div>

## Contributing to the project

**Requirements**:

* Go version >= 1.22.x

To contribute, clone this repo
and install dependencies

Remember to run and add unit tests. For end-to-end tests, you can add your Logz.io parameters to `fluent-bit.conf` and run:
Replace <<arch-type>> with amd or arm
```shell
docker build -t logzio-bit-test -f test/Dockerfile.<<arch-type>> .
docker run logzio-bit-test
```

Always confirm your logs are arriving at your Logz.io account.


## Change log
- **0.6.3**:
  - Fix potential stack overflow: Reduced default bulk size to 2MB, added `logzio_bulk_size_mb` config (1-9 MB).
  - Automate multi-platform binary release assets
- **0.6.2**:
  - Resolve bug with exit code handling to ensure all buffered logs are flushed before termination.
  - Upgrade golang to `1.22`.
  - Upgrade fluent-bit official image to `3.1.4`.
- **0.6.1**:
    - Added support for custom HTTP headers.
    - Validation for malformed headers, duplicate keys, and logging of warnings.
- **0.6.0**:
    - Upgrade fluent-bit official image to v3.0.4
- **0.5.0**:
    - Added HTTP proxy support.
    - Added Array fields support
    - Improved retries.
- **0.4.1**:
    - Trim the compiler build path from stack traces.
- **0.4.0**:
    - Add timestamp decode support for new fluentbit versions.
    - Update to fluent-bit `2.1.9` in docker image.
- **0.3.0**:
    - Added an optional dedot processing.
    - Upgraded to golang `1.19.1.` in docker image.
    - Update to fluent-bit `2.0.8` in docker image.
- **0.2.0**:
    - Added `id` parameter to support multiple outputs.
- **0.1.0**:
    - Upgrade to use Go modules (Thanks @camal-cakar-gcx)
    - Update to fluent-bit `1.8.3` in docker image.
    - Update to latest fluent-bit-go.
    - Upgrade to `gopkg.in/yaml.v3`.
- **0.0.2**:
    - Upgrade to fluent-bit 1.5.4 in docker image (Thanks @alysivji).
    - Fixed error output (Thanks @alexjurkiewicz).
- **0.0.1**:
    - Initial release.
