> Docs: https://developers.mypos.com/sdks/android-sdk/terminal-management

## Activate a Terminal

```java
posHandler.activateTerminal(
    activity,
    new OnTerminalActivationListener() {
        @Override
        public void onActivated() {
            // Terminal is now active and ready for payments
        }

        @Override
        public void onError(int errorCode) {
            // Activation failed
        }
    }
);
```

## Deactivate a Terminal

```java
posHandler.deactivateTerminal(activity, new OnTerminalDeactivationListener() {
    @Override
    public void onDeactivated() { }

    @Override
    public void onError(int errorCode) { }
});
```

## Check for Software Updates

```java
posHandler.checkForSoftwareUpdate(activity, new OnSoftwareUpdateListener() {
    @Override
    public void onUpdateAvailable(String version) {
        posHandler.startSoftwareUpdate(activity, listener);
    }

    @Override
    public void onUpToDate() { }
});
```

## Reading Order

1. `references/connection.md` — connect to terminal first.
2. This file — terminal lifecycle management.

## See Also

- references/connection.md
- https://developers.mypos.com/sdks/android-sdk/terminal-management
