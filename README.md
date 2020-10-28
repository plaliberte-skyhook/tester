# Skyhook Embedded Client Library

## Table of Contents

   * [Skyhook Embedded Client Library](#skyhook-embedded-client-library)
      * [Description](#description)
      * [Building](#building)
         * [Dependencies](#dependencies)
            * [Git Submodules](#git-submodules)
         * [Library Source Files](#library-source-files)
         * [Build Directions](#build-directions)
      * [API Guide](#api-guide)
         * [Summary](#summary)
         * [Requirments](#requirments)
         * [API conventions](#api-conventions)
         * [Client Configuration](#client-configuration)
         * [General Sequence of Operations](#general-sequence-of-operations)
         * [API Reference](#api-reference)
            * [sky_open() - Initialize Skyhook library and verify access to resources](#sky_open---initialize-skyhook-library-and-verify-access-to-resources)
            * [sky_sizeof_state() - Get the size of the non-volatile memory required to save and restore the library state.](#sky_sizeof_state---get-the-size-of-the-non-volatile-memory-required-to-save-and-restore-the-library-state)
            * [sky_sizeof_workspace() - Determines the size of the work space required to process the request](#sky_sizeof_workspace---determines-the-size-of-the-work-space-required-to-process-the-request)
            * [sky_new_request() - Initializes work space for a new request](#sky_new_request---initializes-work-space-for-a-new-request)
            * [sky_add_ap_beacon() - Add a Wi-Fi beacon to request context](#sky_add_ap_beacon---add-a-wi-fi-beacon-to-request-context)
            * [sky_add_cell_lte_beacon() - Add an lte or lte-CatM1 cell beacon to request context](#sky_add_cell_lte_beacon---add-an-lte-or-lte-catm1-cell-beacon-to-request-context)
            * [sky_add_cell_lte_neighbor_beacon() - Adds an LTE neighbor cell beacon to the request context](#sky_add_cell_lte_neighbor_beacon---adds-an-lte-neighbor-cell-beacon-to-the-request-context)
            * [sky_add_cell_gsm_beacon() - Adds a gsm cell beacon to the request context](#sky_add_cell_gsm_beacon---adds-a-gsm-cell-beacon-to-the-request-context)
            * [sky_add_cell_umts_beacon() - Adds a umts cell beacon to the request context](#sky_add_cell_umts_beacon---adds-a-umts-cell-beacon-to-the-request-context)
            * [sky_add_cell_umts_neighbor_beacon() - Adds a umts neighbor cell beacon to the request context](#sky_add_cell_umts_neighbor_beacon---adds-a-umts-neighbor-cell-beacon-to-the-request-context)
            * [sky_add_cell_cdma_beacon() - Adds a cdma cell beacon to the request context](#sky_add_cell_cdma_beacon---adds-a-cdma-cell-beacon-to-the-request-context)
            * [sky_add_cell_nb_iot_beacon() - Adds a nb_iot cell beacon to the request context](#sky_add_cell_nb_iot_beacon---adds-a-nb_iot-cell-beacon-to-the-request-context)
            * [sky_add_cell_nb_iot_neighbor_beacon() - Adds a neighbor nb_iot cell beacon to the request context](#sky_add_cell_nb_iot_neighbor_beacon---adds-a-neighbor-nb_iot-cell-beacon-to-the-request-context)
            * [sky_add_cell_nr_beacon() - Adds a nr cell beacon to the request context](#sky_add_cell_nr_beacon---adds-a-nr-cell-beacon-to-the-request-context)
            * [sky_add_cell_nr_neighbor_beacon() - Adds a neighbor nr cell beacon to the request context](#sky_add_cell_nr_neighbor_beacon---adds-a-neighbor-nr-cell-beacon-to-the-request-context)
            * [sky_add_gnss() - Adds the position of the device from GNSS (GPS, GLONASS, or others) to the request context](#sky_add_gnss---adds-the-position-of-the-device-from-gnss-gps-glonass-or-others-to-the-request-context)
            * [sky_sizeof_request_buf()  - determines the size of the network request buffer which must be provided by the user](#sky_sizeof_request_buf----determines-the-size-of-the-network-request-buffer-which-must-be-provided-by-the-user)
            * [sky_finalize_request() - generate a Skyhook request from the request context](#sky_finalize_request---generate-a-skyhook-request-from-the-request-context)
            * [sky_decode_response() - decodes a Skyhook server response](#sky_decode_response---decodes-a-skyhook-server-response)
            * [sky_perror() - returns a string which describes the meaning of sky_errno codes](#sky_perror---returns-a-string-which-describes-the-meaning-of-sky_errno-codes)
            * [sky_pbeacon() - returns a string which describes the type of a beacon](#sky_pbeacon---returns-a-string-which-describes-the-type-of-a-beacon)
            * [sky_pserver_status() - returns a string which describes the meaning of status codes](#sky_pserver_status---returns-a-string-which-describes-the-meaning-of-status-codes)
            * [sky_close() - frees any resources in use by the Skyhook library](#sky_close---frees-any-resources-in-use-by-the-skyhook-library)
         * [Appendix](#appendix)
            * [API Return Codes](#api-return-codes)
            * [API Error Codes](#api-error-codes)
            * [API Location Result](#api-location-result)
            * [API sky_finalize_request() result](#api-sky_finalize_request-result)
            * [API Logging levels](#api-logging-levels)
      * [License](#license)

## Description

The Skyhook Embedded Client is a small library written in C. It is intended to be
included in embedded targets (e.g., IoT devices) to allow those devices to use
the Skyhook Precision Location service in order to obtain an estimate of the
geolocation of the device on which it runs. This repo also includes a sample client application
which illustrates how the library can be used by your application (see below).

Instructions for cloning and building the library are below.

## Building

### Dependencies

#### Git Submodules

The library depends on several third-party repos which are included as
submodules in this repo. In order to ensure that these dependency repos are
properly populated, be sure to add the `--recursive` option to the `git
clone` command when cloning this repo.

All submodules are contained within the `.submodules` directory.

### Library Source Files

The library includes the following C source files:

* All `.h` and `.c` files in the `libel/` directory
* All `.h` and `.c` files in the `libel/protocol` directory (note that `el.pb.h`
  and `el.pb.c` are generated at build time by the protocol buffers compiler)
* `.submodules/tiney-AES128-C/aes.{h,c}`
* `.submodules/nanopb/pb_common.c`
* `.submodules/nanopb/pb_encode.c`
* `.submodules/nanopb/pb_decode.c`

### Build Directions

To build just the library in a Unix-like environment:

    $ make

This creates the file `bin/libel.a`, which can then be statically linked into
your executable (in a Unix-like environment). Of course it may be necessary to
modify the provided Makefile (or replace it altogether) in order to build the
library for your platform.

To build and run the sample client (after building the library itself):

    $ cd sample_client
    $ make
    $ ./sample_client sample_client.conf

Note that `sample_client.conf` will likely require modification (to add your
Skyhook AES key and partner ID).

## API Guide

### Summary

Skyhook's Embedded Library API for IoT provides an interface for the Client Application to build a Skyhook Location Request and get an immediate location response when the IoT device is stationary (or in a remembered location), otherwise, an encoded request is returned to the client that should be sent to the ELG server. The library also provides decoding and extracting of information from the ELG server response message.

Requests consist of a set of beacons (Wi-Fi Access Points and Cell towers) which are within range of the client device. These are package into the request and the Skyhook server processes the available information to determine the most accurate location. The library balances accuracy of location against bandwidth of communication with the Skyhook server, by having a maximum size of request that can be sent to the server. This is achieved as each Wi-Fi or Cellular beacon is added to the request; the library selectively keeps or discards beacon information based on a carefully crafted algorithm.

The library will save the information provided in a request along with the location determined by the Skyhook server in a cache and whenever subsequent requests are a reasonable match to the cache, the library will use the known location to satisfy the request, eliminating the need to communicate with the server for this request.

### Requirments

 1. Low memory use, both code and data size.
 1. Low bandwidth i.e. Small messages infrequently.
 1. Avoid platform specific dependencies i.e. ELG library will be written to be portable to many platforms.

### API conventions
 * User is responsible for providing the work space for any given request/response transaction.
 * The address of this work space is considered the unique identifier for a given location request/response transaction.
 * If more than one beacon is added with the connected flag = yes, only the most recent beacon is considered to be connected.
 * sky_errno is always set, either to SKY_ERROR_NONE or to the error code.
 * The user may free the allocated work space after receiving a location (lat/long) from either sky_finalize_request or sky_decode_response, or an error from the API. 
 * If time() returns a date prior to March 1, 2019, API will not match cached scans/locations.
 * The library includes SKY_LOG_LEVEL_DEBUG logging by default to assist with integration efforts. To remove this, build the library with SKY_DEBUG false. Passing a min_level value to sky_open() allows intermediate levels of logging.
 * The library is not thread safe.
 * User may preserve cache state by storing the state buffer provided by the call to sky_close(), and restoring the state buffer and passing it to sky_open().

### Client Configuration

Your embedded client needs to be configured with a Skyhook partner ID and AES (encryption) key. You can obtain these parameters via the following steps:

 1. Register a new account on http://my.skyhookwireless.com
 1. Once registration is completed you will be placed immediately into the “Start a new project” workflow.
 1. Create a new Precision Location project and name the project whatever you like.
 1. After creating the project, in the platform selection screen, select the “ELG” embedded project type which can be found below the standard OS selection icons.
 1. Once your new embedded project has been created, your partner ID and AES key will be visible on the right side of your project home screen.

|Description                  |  Value                                      |
|:----------------------------|:--------------------------------------------|
|                             |                                             |
| Skyhook API key             |"1E5D0AAEA1DEC1CD10DC2DD1CA11CE6B" (Example) |
| Partner ID                  |1 (Example)                                  |
| Skyhook ELG Server hostname|"elg.skyhook.com"                             |
| Skyhook ELG Server port     |9756                                         |

   **Note** These value must be stored within the client application and passed to sky_open(). 

The Skyhook Embedded Library creates (see sky_finalize_request()) and interprets (see sky_decode_response()) messages which are exchanged via a simple TCP (not HTTP) connection established by the client device (the "user") to the Skyhook Embedded Library Gateway (ELG) server. This message exchange must be done once for each call to sky_finalize_request() (unless the library is able to use a previously cached location). Because they are usually platform-dependent, the details associated with the establishment of this connection, and the associated send/receive mechanisms, are the responsibility of the user. See the sample_client directory within the library repo for an example of how this can be done using standard socket API calls.

In general, the user must take the following steps in order to perform this exchange:

 1. Establish a TCP connection to the configured host and port number (see the Configuration Parameters table above)
 1. Send the request
 1. Wait for and then receive the response (use whatever timeout value is appropriate for the network in which the device is expected to run; the additional latency overhead imposed by the Skyhook service itself should be well under one second)
 1. Close the TCP connection

### General Sequence of Operations
![missing image](https://github.com/SkyhookWireless/embedded-client/blob/master/images/elg_embedded_image.png?raw=true)

Figure 1 - The User is expected to make a sequence of calls like the following

## API Reference

### sky_open() - Initialize Skyhook library and verify access to resources

```c
Sky_status_t sky_open(Sky_errno_t *sky_errno,
    uint8_t *device_id,
    uint32_t id_len,
    uint32_t partner_id,
    uint8_t aes_key[16],
    void *state_buf,
    Sky_log_level_t min_level,
    Sky_loggerfn_t logf,
    Sky_randfn_t rand_bytes,
    Sky_timefn_t gettime
)
 
/* Parameters
 * sky_errno    if sky_open returns failure, sky_errno is set to the error code
 * device_id    Device unique ID (example mac address of the device)
 * id_len       length if the Device ID, typically 6, Max 16 bytes
 * partner_id   Skyhook assigned credentials
 * aes_key      Skyhook assigned encryption key
 * state_buf    pointer to a state buffer (provided by sky_close) or NULL
 * min_level    logging function is called for msg with equal or greater level
 * logf         pointer to logging function
 * rand_bytes   pointer to random function
 * gettime      pointer to time function
 *
 * Returns      SKY_SUCCESS or SKY_ERROR and sets sky_errno with error code
 */
 ```
Called once to set up any resources needed by the library (e.g. cache space). Returns SKY_SUCCESS on success, SKY_ERROR on error and sets sky_errno. If state_buf is not NULL and points to a valid state buffer, it is restored and populates the cache. If sky_state is not NULL and points to a invalid state buffer it is considered an error (SKY_ERROR_BAD_STATE). If logf() is not NULL, the log messages will be generated if they were turned on during compilation (SKY_DEBUG). If rand_bytes is not NULL, this function pointer is used to generate random sequences of bytes, otherwise the library calls rand(). If min_level can be set to block less severe log messages, e.g. if min_level is set to SKY_LOG_LEVEL_ERROR, only log messages with level SYK_LOG_LEVEL_CRITICAL and SKY_LOG_LEVEL_ERROR will be generated. If gettime is not NULL, this function pointer is used to request the current time (Unix time aka POSIX time aka UNIX **Epoch** time) , otherwise the library calls time().

### sky_sizeof_state() - Get the size of the non-volatile memory required to save and restore the library state.

```c
int32_t sky_sizeof_state(void *sky_state)

/* Parameters
 * sky_state        Pointer to state buffer

 * Returns          Size of state buffer or 0 to indicate that the buffer was invalid
 */
 ```
Determines the size of the non-volatile memory that will be required to save and restore the library state. This will preserve any cached scan data with associated location information. It may be called at any time to determine either the space used by a state buffer that has been copied to non-volatile memory, or the size of the buffer returned by sky_close.

### sky_sizeof_workspace() - Determines the size of the work space required to process the request

```c
int32_t sky_sizeof_workspace(void)

/* Returns          Size of workspace buffer required
 */
```
Reports the number of bytes of work space buffer required to handle encoding the request. This should be allocated by the caller and a pointer to this allocated space passed to sky_new_request(). This space is used to accumulate the beacon information to form the server request.

### sky_new_request() - Initializes work space for a new request

```c
Sky_ctx_t* sky_new_request(void *workspace_buf,
    uint32_t bufsize,
    Sky_errno_t *sky_errno
)

/* Parameters
 * workspace_buf        Pointer to workspace provided by user
 * bufsize              Workspace buffer size (from sky_sizeof_workspace)
 * sky_errno            Pointer to error code

 * Returns              Pointer to the initialized workspace context buffer or NULL
 */
```
Initializes work space for a new request. Returns request context (pointer to the work space) or NULL in case of failure. In case of failure sky_errno is set to indicate the error. User can decode the error using sky_perror(). Initializes request context with Magic numbers.

### sky_add_ap_beacon() - Add a Wi-Fi beacon to request context

```c
Sky_status_t sky_add_ap_beacon(Sky_ctx_t *ctx,
    Sky_errno_t *sky_errno,
    uint8_t mac[6],
    time_t timestamp,
    int16_t rssi,
    int32_t frequency,
    bool is_connected
)

/* Parameters
 * ctx          Skyhook request context
 * sky_errno    sky_errno is set to the error code
 * mac          pointer to mac address of the Wi-Fi beacon
 * timestamp    time in seconds (from 1970 epoch) indicating when the scan was performed, (time_t)-1 if unknown
 * rssi         Received Signal Strength Intensity, -10 through -127, -1 if unknown
 * frequency    Center Frequency of Channel in MHz, 2400 through 6000, -1 if unknown
 * is_connected this beacon is currently connected, false if unknown

 * Returns      SKY_SUCCESS or SKY_ERROR and sets sky_errno with error code
 */
 ```
Adds the Wi-Fi access point information to the request context. Returns SKY_ERROR for failure or the SKY_SUCCESS.  In case of failure sky_error is set to error code. If enough Wi-Fi beacons are already in the request context, sky_add_ap_beacon() may remove the least useful beacon.

### sky_add_cell_lte_beacon() - Add an lte or lte-CatM1 cell beacon to request context

```c


Sky_status_t sky_add_cell_lte_beacon(Sky_ctx_t *ctx,
    Sky_errno_t *sky_errno,
    int32_t tac,
    int64_t e_cellid,
    uint16_t mcc,
    uint16_t mnc,
    int16_t pci,
    int32_t earfcn,
    time_t timestamp,
    int16_t rsrp,
    bool is_connected
)
 
/* Parameters
 * ctx          Skyhook request context
 * sky_errno    sky_errno is set to the error code
 * tac          lte tracking area code identifier (1-65535), SKY_UNKNOWN_ID3 if unknown
 * e_cellid     lte beacon identifier 28bit (0-268435455)
 * mcc          mobile country code (200-799)
 * mnc          mobile network code (0-999)
 * pci          mobile pci (0-503, SKY_UNKNOWN_ID5 if unknown)
 * earfcn,      channel (0-45589, SKY_UNKNOWN_ID6 if unknown)
 * timestamp    time in seconds (from 1970 epoch) indicating when the scan was performed, (time_t)-1 if unknown
 * rsrp         Received Signal Receive Power, range -140 to -40dbm, -1 if unknown
 * is_connected this beacon is currently connected, false if unknown
 
 * Returns      SKY_SUCCESS or SKY_ERROR and sets sky_errno with error code
 */
```
Adds the cell lte or lte-CatM1  information to the request context. Returns SKY_ERROR for failure or the SKY_SUCCESS.  In case of failure sky_error is set if there is not enough room in the request context or other error. 

### sky_add_cell_lte_neighbor_beacon() - Adds an LTE neighbor cell beacon to the request context

```c
Sky_status_t sky_add_cell_lte_neighbor_beacon(Sky_ctx_t *ctx,
    Sky_errno_t *sky_errno,
    int16_t pci,
    int32_t earfcn,
    time_t timestamp,
    int16_t rsrp)
 
/* Parameters
 * ctx          Skyhook request context
 * sky_errno    sky_errno is set to the error code
 * pci          mobile pci (0-503, SKY_UNKNOWN_ID5 if unknown)
 * earfcn,      channel (0-45589, SKY_UNKNOWN_ID6 if unknown)
 * timestamp    time in seconds (from 1970 epoch) indicating when the scan was performed, (time_t)-1 if unknown
 * rsrp         Received Signal Receive Power, range -140 to -40dbm, -1 if unknown
 
 * Returns      SKY_SUCCESS or SKY_ERROR and sets sky_errno with error code
 */

 ```

Adds the lte or lte-CatM1 neighbor cell information to the request context. Returns SKY_ERROR for failure or the SKY_SUCCESS.  In case of failure sky_error is set if there is not enough room in the request context or other error.

### sky_add_cell_gsm_beacon() - Adds a gsm cell beacon to the request context

```c
Sky_status_t sky_add_cell_gsm_beacon(Sky_ctx_t * ctx,
    Sky_errno_t *sky_errno,
    int32_t lac,
    int64_t ci,
    uint16_t mcc,
    uint16_t mnc,
    time_t timestamp,
    int16_t rssi,
    bool is_connected
)
 
/* Parameters
 * ctx          Skyhook request context
 * sky_errno    sky_errno is set to the error code
 * lac          gsm location area code identifier (1-65535)
 * ci           gsm cell identifier (0-65535)
 * mcc          mobile country code (200-799)
 * mnc          mobile network code (0-999)
 * timestamp    time in seconds (from 1970 epoch) indicating when the scan was performed, (time_t)-1 if unknown
 * rssi         Received Signal Strength Intensity, range -128 to -32dbm, -1 if unknown
 * is_connected this beacon is currently connected, false if unknown
 
 * Returns      SKY_SUCCESS or SKY_ERROR and sets sky_errno with error code
 */
 ```
Adds the cell gsm information to the request context. Returns SKY_ERROR for failure or the SKY_SUCCESS.  In case of failure sky_error is set if there is not enough room in the request context or other error. 

### sky_add_cell_umts_beacon() - Adds a umts cell beacon to the request context

```c
Sky_status_t sky_add_cell_umts_beacon(Sky_ctx_t *ctx,
    Sky_errno_t *sky_errno,
    int32_t lac,
    int64_t ucid,
    uint16_t mcc,
    uint16_t mnc,
    int16_t psc,
    int16_t uarfcn,
    time_t timestamp,
    int16_t rscp,
    bool is_connected
)
 
/* Parameters
 * ctx          Skyhook request context
 * sky_errno    sky_errno is set to the error code
 * lac          umts location area code identifier (1-65535), SKY_UNKNOWN_ID3 if unknown
 * ucid         umts cell identifier 28bit (0-268435455)
 * mcc          mobile country code (200-799)
 * mnc          mobile network code (0-999)
 * psc          primary scrambling code (0-511), SKY_UNKNOWN_ID5 if unknown
 * uarfcn       channel (412-10838), SKY_UNKNOWN_ID6 if unknown
 * timestamp    time in seconds (from 1970 epoch) indicating when the scan was performed, (time_t)-1 if unknown
 * rscp         Received Signal Code Power, range -120dbm to -20dbm, -1 if unknown
 * is_connected This beacon is currently connected, false if unknown
 
 * Returns      SKY_SUCCESS or SKY_ERROR and sets sky_errno with error code
 */
 ```
Adds the cell umts information to the request context. Returns SKY_ERROR for failure or the SKY_SUCCESS.  In case of failure sky_error is set if there is not enough room in the request context or other error.

### sky_add_cell_umts_neighbor_beacon() - Adds a umts neighbor cell beacon to the request context

```c
Sky_status_t sky_add_cell_umts_neighbor_beacon(Sky_ctx_t *ctx,
    Sky_errno_t *sky_errno,
    int16_t psc,
    int16_t uarfcn,
    time_t timestamp,
    int16_t rscp
)
 
/* Parameters
 * ctx          Skyhook request context
 * sky_errno    sky_errno is set to the error code
 * psc          primary scrambling code (0-511), SKY_UNKNOWN_ID5 if unknown
 * uarfcn       channel (412-10838), SKY_UNKNOWN_ID6 if unknown
 * timestamp    time in seconds (from 1970 epoch) indicating when the scan was performed, (time_t)-1 if unknown
 * rscp         Received Signal Code Power, range -120dbm to -20dbm, -1 if unknown
 
 * Returns      SKY_SUCCESS or SKY_ERROR and sets sky_errno with error code
 */
 ```
Adds the umts neighbor cell information to the request context. Returns SKY_ERROR for failure or the SKY_SUCCESS.  In case of failure sky_error is set if there is not enough room in the request context or other error.

### sky_add_cell_cdma_beacon() - Adds a cdma cell beacon to the request context

```c
Sky_status_t sky_add_cell_cdma_beacon(Sky_ctx_t *ctx,
    Sky_errno_t *sky_errno,
    uint32_t sid,
    int32_t nid,
    int64_t bsid,
    time_t timestamp,
    int16_t rssi,
    bool is_connected
)
 
/* Parameters
 * ctx          Skyhook request context
 * sky_errno    sky_errno is set to the error code
 * sid          cdma system identifier (0-32767)
 * nid          cdma network identifier(0-65535)
 * bsid         cdma base station identifier (0-65535)
 * timestamp    time in seconds (from 1970 epoch) indicating when the scan was performed, (time_t)-1 if unknown
 * rssi         RSSI (Received Signal Receive Power level) range -140 to -49dbm, -1 if unknown
 * is_connected this beacon is currently connected, false if unknown
 
 * Returns      SKY_SUCCESS or SKY_ERROR and sets sky_errno with error code
 */
 ```
Adds the cell cdma information to the request context. Returns NULL and sets sky_error if there is not enough room in the request context or other error.

### sky_add_cell_nb_iot_beacon() - Adds a nb_iot cell beacon to the request context

```c
Sky_status_t sky_add_cell_nb_iot_beacon(Sky_ctx_t *ctx,
    Sky_errno_t *sky_errno,
    uint16_t mcc,
    uint16_t mnc,
    int64_t e_cellid,
    int32_t tac,
    int16_t ncid,
    int32_t earfcn,
    time_t timestamp,
    int16_t nrsrp,
    bool is_connected
)
 
/* Parameters
 * ctx          Skyhook request context
 * sky_errno    sky_errno is set to the error code
 * mcc          mobile country code (200-799)
 * mnc          mobile network code (0-999)
 * e_cellid     nbiot beacon identifier (0-268435455)
 * tac          nbiot tracking area code identifier (1-65535), SKY_UNKNOWN_ID3 if unknown
 * ncid         mobile cell ID  (0-503), SKY_UNKNOWN_ID5 if unknown
 * earfcn       channel (0-45589), SKY_UNKNOWN_ID6 if unknown
 * timestamp    time in seconds (from 1970 epoch) indicating when the scan was performed, (time_t)-1 if unknown
 * nrsrp        Narrowband Reference Signal Received Power, range -156 to -44dbm, -1 if unknown
 * is_connected this beacon is currently connected, false if unknown
 
 * Returns      SKY_SUCCESS or SKY_ERROR and sets sky_errno with error code
 */
 ```
Adds the cell nb-IoT information to the request context. Returns SKY_ERROR for failure or the SKY_SUCCESS.  In case of failure sky_error is set if there is not enough room in the request context or other error.

### sky_add_cell_nb_iot_neighbor_beacon() - Adds a neighbor nb_iot cell beacon to the request context

```c
Sky_status_t sky_add_cell_nb_iot_neighbor_beacon(Sky_ctx_t *ctx,
    Sky_errno_t *sky_errno,
    int16_t ncid,
    int32_t earfcn,
    time_t timestamp,
    int16_t nrsrp
)
 
/* Parameters
 * ctx          Skyhook request context
 * sky_errno    sky_errno is set to the error code
 * ncid         mobile cell ID  (0-503), SKY_UNKNOWN_ID4 if unknown
 * earfcn       channel (0-45589), SKY_UNKNOWN_ID6 if unknown
 * timestamp    time in seconds (from 1970 epoch) indicating when the scan was performed, (time_t)-1 if unknown
 * nrsrp        Narrowband Reference Signal Received Power, range -156 to -44dbm, -1 if unknown
 
 * Returns      SKY_SUCCESS or SKY_ERROR and sets sky_errno with error code
 */
 ```
Adds the nb-IoT neighbor cell information to the request context. Returns SKY_ERROR for failure or the SKY_SUCCESS.  In case of failure sky_error is set if there is not enough room in the request context or other error.

### sky_add_cell_nr_beacon() - Adds a NR cell beacon to the request context

```c
Sky_status_t sky_add_cell_nr_beacon(Sky_ctx_t *ctx,
    Sky_errno_t *sky_errno,
    uint16_t mcc,
    uint16_t mnc,
    int64_t nci,
    int32_t tac,
    int16_t pci,
    int32_t nrarfcn,
    time_t timestamp,
    int16_t nrsrp
)
 
/* Parameters
 * ctx          Skyhook request context
 * sky_errno    sky_errno is set to the error code
 * mcc          mobile country code (200-799)
 * mnc          mobile network code (0-999)
 * nci          nr cell identity (0-68719476735)
 * tac          tracking area code identifier (1-65535), SKY_UNKNOWN_ID3 if unknown
 * pci          physical cell ID (0-1007), SKY_UNKNOWN_ID5 if unknown
 * nrarfcn      channel (0-3279165), SKY_UNKNOWN_ID6 if unknown
 * timestamp    time in seconds (from 1970 epoch) indicating when the scan was performed, (time_t)-1 if unknown
 * nrsrp        Narrowband Reference Signal Received Power, range -156 to -44dbm, -1 if unknown
 * is_connected this beacon is currently connected, false if unknown
 
 * Returns      SKY_SUCCESS or SKY_ERROR and sets sky_errno with error code
 */
 ```
Adds the cell NR information to the request context. Returns SKY_ERROR for failure or the SKY_SUCCESS.  In case of failure sky_error is set if there is not enough room in the request context or other error.

### sky_add_cell_nr_neighbor_beacon() - Adds a neighbor NR cell beacon to the request context

```c
Sky_status_t sky_add_cell_nr_neighbor_beacon(Sky_ctx_t *ctx,
    Sky_errno_t *sky_errno,
    int16_t pci,
    int32_t nrarfcn,
    time_t timestamp,
    int16_t nrsrp
)
 
/* Parameters
 * ctx          Skyhook request context
 * sky_errno    sky_errno is set to the error code
 * pci          physical cell ID (0-1007), SKY_UNKNOWN_ID5 if unknown
 * nrarfcn      channel (0-3279165), SKY_UNKNOWN_ID6 if unknown
 * timestamp    time in seconds (from 1970 epoch) indicating when the scan was performed, (time_t)-1 if unknown
 * nrsrp        Narrowband Reference Signal Received Power, range -156 to -44dbm, -1 if unknown
 
 * Returns      SKY_SUCCESS or SKY_ERROR and sets sky_errno with error code
 */
 ```
Adds the NR neighbor cell information to the request context. Returns SKY_ERROR for failure or the SKY_SUCCESS.  In case of failure sky_error is set if there is not enough room in the request context or other error.

### sky_add_gnss() - Adds the position of the device from GNSS (GPS, GLONASS, or others) to the request context


```c
sky_status_t sky_add_gnss(sky_ctx_t *ctx,
sky_errno_t *sky_errno,
float lat,
float lon,
uint16_t hpe,
float altitude,
uint16_t vpe,
float speed,
float bearing,
uint16_t nsat,
time_t timestamp)
 
/* Parameters
 * ctx          Skyhook request context
 * sky_errno    sky_errno is set to the error code
 * lat          device latitude in degrees, NaN if unknown
 * lon          device longitude in degrees, NaN if unknown
 * hpe          pointer to horizontal Positioning Error in meters with 68% confidence, 0 if unknown
 * altitude     pointer to altitude above mean sea level, in meters, NaN if unknown
 * vpe          pointer to vertical Positioning Error in meters with 68% confidence, 0 if unknown
 * speed        pointer to speed in meters per second, Nan if unknown
 * bearing      pointer to bearing of device in degrees, counterclockwise from north
 * nsat         number of satellites used to determine location, 0 if unknown
 * timestamp    time in seconds (from 1970 epoch) indicating when the scan was performed, (time_t)-1 if unknown
 
 * Returns      SKY_SUCCESS or SKY_ERROR and sets sky_errno with error code
 */
 ```
Adds the GNSS information to the request context. Returns SKY_ERROR for failure or the SKY_SUCCESS.  In case of failure sky_error is set to error code. 

### sky_sizeof_request_buf()  - determines the size of the network request buffer which must be provided by the user


```c
Sky_status_t sky_sizeof_request_buf(Sky_ctx_t *ctx, 
    uint32_t* size,
    Sky_errno_t *sky_errno
)
 
/*
 * Parameters
 * ctx              Skyhook request context
 * sky_errno        sky_errno is set to the error code
 * size             Request size in bytes stored here
 
 * Returns          SKY_SUCCESS or SKY_ERROR and sets sky_errno with error code
 */
 ```
Called after all beacon and GNSS data has been added (via the sky_add_*() functions) in order to determine the size of the request buffer that must be allocated by the user.

### sky_finalize_request() - generate a Skyhook request from the request context

```c
Sky_finalize_t sky_finalize_request(Sky_ctx_t *ctx,
    Sky_errno_t *sky_errno,
    void *request_buf,
    uint32_t bufsize,
    Sky_location_t *loc,
    uint32_t *response_size
)
 
/* Parameters
 * ctx              Skyhook request context
 * sky_errno        sky_errno is set to the error code
 * request_buf      Request buffer (allocated by the user) into which the Skyhook server request will be encoded
 * bufsize          Request buffer size in bytes (should be set to the result of the call to sky_sizeof_request_buf() function)
 * loc              Pointer to the structure where latitude, longitude are written
 * response_size    the space required to hold the server response
 
 * Returns          SKY_FINALIZE_REQUEST, SKY_FINALIZE_LOCATION or SKY_FINALIZE_ERROR and sets sky_errno with error code
 */
 ```
Returns SKY_FINALIZE_ERROR and sets sky_error if an error occurs. If the result is SKY_FINALIZE_REQUEST, the request buffer is filled in with the serialized request data which the user must then send to the Skyhook server, and the response_size is set to the maximum buffer size needed to receive the Skyhook server response. If the result is SKY_FINALIZE_LOCATION, the location (lat, lon, hpe and source) are filled in from a previously successful server response held in the cache. 

### sky_decode_response() - decodes a Skyhook server response

```c
Sky_status_t sky_decode_response(Sky_ctx_t *ctx,
    Sky_errno_t *sky_errno,
    void *response_buf,
    uint32_t bufsize,
    Sky_location_t *loc
)
 
/* Parameters
 * ctx              Skyhook request context
 * sky_errno        sky_errno is set to the error code
 * response_buf     buffer holding the skyhook server response
 * bufsize          Size of response buffer in bytes as returned by sky_finalize_request
 * loc              where to save device latitude, longitude etc from cache if known
 
 * Returns          SKY_SUCCESS or SKY_ERROR and sets sky_errno with error code
 */
```
User calls this to process the network response from the Skyhook server. Returns SKY_ERROR and sets sky_error if the response buffer could not be decoded or other error occurred, otherwise SKY_SUCCESS is returned. Cache is updated with scan and location information. Note that sky_decode_response() modifies the response buffer (it is written with decrypted bytes). A DEBUG level log message is available to help diagnose any errors that may happen 

### sky_perror() - returns a string which describes the meaning of sky_errno codes

```c
char* sky_perror(sky_errno_t sky_errno)
 
/* Parameter
 * sky_errno        Error code for which to provide descriptive string
  
 * Returns          pointer to string or NULL if the code is invalid
 */
```
Return a static string which describes the meaning of the value passed in sky_errno, or "Unknown error" if sky_errno has an unexpected value.

### sky_pbeacon() - returns a string which describes the type of a beacon

```c
char* sky_pbeacon(Beacon_t *beacon)
 
/* Parameter
 * beacon           pointer to Beacon_t structure for which to provide descriptive string
  
 * Returns          pointer to string or NULL if the code is invalid
 */
```
Return a static string which describes the type of the beacon passed in pointer beacon, or "Unknown" if beacon type has an unexpected value.

### sky_pserver_status() - returns a string which describes the meaning of status codes

```c
char* sky_pserver_status(sky_server_status_t status)
 
/* Parameter
 * status           Status code for which to provide descriptive string
  
 * Returns          pointer to string or NULL if the status is invalid
 */
```
Return a static string which describes the meaning of the value passed in status, or "Unknown server status" if status has an unexpected value. status is available in the location_status field of the Sky_location_t structure when sky_decode_response() returns SKY_SUCCESS. 

### sky_close() - frees any resources in use by the Skyhook library

```c
Sky_status_t sky_close(Sky_errno_t *sky_errno,
    void **sky_state
)
 
/* Parameters
 * sky_errno        sky_errno is set to the error code
 * sky_state        pointer to where the state buffer reference should be stored
 
 * Returns          SKY_SUCCESS or SKY_ERROR and sets sky_errno with error code
 */
```
Returns SKY_SUCCESS on success, SKY_ERROR on error and sets sky_errno appropriately. If sky_state was provided (not NULL), it will be set to point to sky_sizeof_state bytes of state buffer, which should be saved to non-volatile memory, 

### Appendix
### API Return Codes
`Sky_status_t` indicates the status of a completed API request:

| Return Code                                      | Description
| ------------------------------------------------ | --------------------------------------------------------------
| `SKY_SUCCESS`                                    | Operation was successful
| `SKY_ERROR`                                      | Operation failed with sky_errno set

### API Error Codes
`Sky_errno_t` indicates the error condition:

| Error Code                                      | Description
| ----------------------------------------------- | --------------------------------------------------------------
| `SKY_ERROR_NONE`                                | No error
| `SKY_ERROR_NEVER_OPEN`                          | sky_open must be called before the current operation can succeed
| `SKY_ERROR_ALREADY_OPEN`                        | sky_open called more than once with different parameters. Call sky_close
| `SKY_ERROR_BAD_PARAMETERS`                      | The parameters to the current operation are illegal
| `SKY_ERROR_TOO_MANY`                            | There are not enough resources for this operation
| `SKY_ERROR_BAD_WORKSPACE`                       | The workspace ctx structure is corrupt
| `SKY_ERROR_BAD_STATE`                           | The cache state buffer is corrupt
| `SKY_ERROR_DECODE_ERROR`                        | Could not decode the the response buffer
| `SKY_ERROR_ENCODE_ERROR`                        | Could not encode the request
| `SKY_ERROR_RESOURCE_UNAVAILABLE`                | Could not acquire the necessary resourses
| `SKY_ERROR_CLOSE`                               | Failed to cleanup resources during close
| `SKY_ERROR_BAD_KEY`                             | AES_Key is not valid format
| `SKY_ERROR_NO_BEACONS`                          | Beacons must be added before a request can be finalized
| `SKY_ERROR_ADD_CACHE`                           | Internal error trying to add a location to the cache state
| `SKY_ERROR_GET_CACHE`                           | Internal error trying to find a location in the cache state
| `SKY_ERROR_LOCATION_UNKNOWN`                    | Server response indicates no location could be determined
| `SKY_ERROR_SERVER_ERROR`                        | Server sent a response which indicated an error processing the request

### API Location Result
The library returns location information as `Sky_location_t`:

```c

typedef struct sky_location {
    float lat, lon; /* GNSS info */
    uint16_t hpe;
    uint32_t time;
    Sky_loc_source_t location_source;
    Sky_loc_status_t location_status;
} Sky_location_t;
```
The location_source field can have one of the following values:

| Location Source                                 | Description
| ----------------------------------------------- | --------------------------------------------------------------
| `SKY_LOCATION_STATUS_SUCCESS`                   | location was determined successfully
| `SKY_LOCATION_STATUS_UNSPECIFIED_ERROR`         | Server reported an unspecified error
| `SKY_LOCATION_STATUS_BAD_PARTNER_ID_ERROR `     | Server reported an error looking up partner ID
| `SKY_LOCATION_STATUS_DECODE_ERROR`              | Server reported an error while decoding request
| `SKY_LOCATION_STATUS_API_SERVER_ERROR`          | Server reported an error while processing request

### API sky_finalize_request() result
sky_finalize_request() returns a result as`Sky_finalize_t`:

| sky_finalize_request result                     | Description
| ----------------------------------------------- | --------------------------------------------------------------
| `SKY_FINALIZE_ERROR`                            | Unable to process request context
| `SKY_FINALIZE_LOCATION`                         | The location is known e.g. stationary
| `SKY_FINALIZE_REQUEST`                          | Context contains a server request

### API Logging levels
The level at which the library generates log messages can be configured by passing `min_level` with one of these values to `sky_open()`:

| Logging Level                                   | Description
| ----------------------------------------------- | --------------------------------------------------------------
| `SKY_LOG_LEVEL_CRITICAL`                        | Information about an error which caused library to immediately close
| `SKY_LOG_LEVEL_ERROR`                           | Information about an operation which failed
| `SKY_LOG_LEVEL_WARNING`                         | Information about an operation which is continuing with unexpected condition
| `SKY_LOG_LEVEL_DEBUG`                           | Verbose information about the operation being performed

**Note** the above table is in decending severity order

Setting min_level blocks less severe log messages. A level of `SKY_LOG_LEVEL_DEBUG` will include all logging, 'SKY_LOG_LEVEL_ERROR' will include error and critical log messages.

License
-------

This project is licensed under the MIT license. See LICENSE.txt.
