# About


# References

- https://github.com/Bosch-SW/linux-iccom
- https://github.com/Bosch-SW/libiccom

# iccom-usage

## Module loading

```
# modprobe iccom_socket_if
```

Check loaded modules:

```
# lsmod
iccom_socket_if        16384 0
iccom_transport_helper 16384 1 iccom_socket_if
iccom                  32768 1 iccom_socket_if
```

## Loopback configuration

If using loopback, you should configure loopback channel mapping.
More detail, see https://github.com/Bosch-SW/linux-iccom/blob/6abd5ca9ff80f4bdffdec18776c30725b7e9d77b/src/iccom_socket_if.c#L156-L174

```
// The struct which described the loopback mapping rule.
//
//          shift
//    |------------------->|
//    |                    |
// ---[-------]------------[-------]----------
//  from     to          from     to
//                     + shift  + shift
//
//    |-------|            |-------|
//     original            loopback
//     channel              mapped
//      range               range
//
// @from {<= to} the first port to loopback (inclusive)
// @to {>= from} the last port to loopback (inclusive)
// @shift defines a window where to make a loopback
//     ports.
//     NOTE: if shift == 0, then rule is no active
```

For example, to map ch.0 ~ ch.9 to ch.10 ~ ch.19, set 'from' to 0, 'to' to 9, 'shift' to 10.


There are two ways for setup loopback:
- Using procfs
- Using libiccom

### Using procfs

Write to /proc/iccomif/loopbackctl:

```
# echo 0 9 10 > /proc/iccommif/loopbackctl
```

### Using libiccom

Call iccom_loopback_enable(), for example:

```
int from_ch = 0;
int to_ch = 9;
int shift = 10;
int ret;

ret = iccom_loopback_enable(from_ch, to_ch, shift);
if (ret) {
    perror("iccom_loopback_enable failed");
}
```

# Send/Receive message

* C language wrapper basically simply removes whole boilerplate about
  sending netlink messages and provides among others following functions:
  * `int iccom_open_socket(const unsigned int channel);` - to open an ICCom
     socket.
  * `void iccom_close_socket(const int sock_fd);` - to close the ICCom socket.
  * `int iccom_send_data(...)` - to send the given data.
  * `int iccom_receive_data(...)` - to receive the data from ICCom socket.


more detail, see https://github.com/Bosch-SW/libiccom/blob/main/readme.md

For example, the following is an implementation example of 
sender using ch.1 and receiver using ch.11 (loopback):

## Sender


## Receiver

Noted that `offset_out`, 
## restriction

Max message size
#define ICCOM_SOCKET_MAX_MESSAGE_SIZE_BYTES 4096
