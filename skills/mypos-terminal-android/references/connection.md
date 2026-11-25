> Docs: https://developers.mypos.com/sdks/android-sdk/integration
> POS info: https://developers.mypos.com/sdks/android-sdk/pos-info-statuses

## Bluetooth Connection

```java
POSHandler posHandler = POSHandler.getInstance();

// Connect by terminal serial number
posHandler.connectDevice(context, "TERMINAL_SERIAL_NUMBER");

// Register connection listener
posHandler.setOnBindListener(new OnBindListener() {
    @Override
    public void onBind() {
        // Terminal connected and ready
    }

    @Override
    public void onUnbind() {
        // Terminal disconnected — handle reconnect if needed
    }
});
```

## USB Connection

```java
posHandler.connectDevice(context, "TERMINAL_SERIAL_NUMBER",
    ConnectionType.USB);
```

## TCP-IP Connection

```java
posHandler.connectDevice(context, "192.168.1.100",
    ConnectionType.TCP_IP);
// Pass the terminal's local IP address instead of serial number
```

## Discover Available Bluetooth Terminals

```java
posHandler.startDiscovery(new OnDiscoveryListener() {
    @Override
    public void onDeviceFound(BluetoothDevice device, String serialNumber) {
        // device found — present to user for selection
    }
});
```

## Disconnect

```java
posHandler.disconnectDevice();
```

## Reading Order

1. `references/setup.md` — initialize POSHandler first.
2. This file — establish connection.
3. `references/payment.md` — initiate payments after connection.

## See Also

- references/setup.md
- references/payment.md
