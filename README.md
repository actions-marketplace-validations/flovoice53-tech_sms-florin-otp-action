# Get SMS/OTP Code (sms-florin)

A GitHub Action that rents a real UK phone number and waits for an SMS/OTP verification code — for testing signup/2FA flows in CI without burning a real phone number on every run.

Powered by [sms-florin](https://flo-voice1.com): actual physical SIM cards on real UK mobile networks (EE/Three), not a VoIP reseller. Pay-as-you-go, no subscription — your first instant rental is free.

## Usage

```yaml
- name: Get a WhatsApp verification code
  id: otp
  uses: flovoice53-tech/sms-florin-otp-action@v1
  with:
    api-key: ${{ secrets.SMS_FLORIN_API_KEY }}
    service-slug: whatsapp

- name: Use the number and code in your test
  run: |
    echo "Phone number: ${{ steps.otp.outputs.phone-number }}"
    echo "Raw SMS: ${{ steps.otp.outputs.sms-body }}"
    # Extract the actual code however your service formats it, e.g.:
    # CODE=$(echo "${{ steps.otp.outputs.sms-body }}" | grep -oE '[0-9]{4,8}' | head -1)
```

Get an API key at [flo-voice1.com/api-access](https://flo-voice1.com/api-access) (free account, verify your email, generate a key) — never hardcode it, store it as a repository secret (`SMS_FLORIN_API_KEY`).

## Inputs

| Name                     | Required | Default                        | Description                                                        |
| ------------------------ | -------- | ------------------------------- | -------------------------------------------------------------------- |
| `api-key`                | yes      | —                                | Your sms-florin API key.                                            |
| `service-slug`           | yes      | —                                | Which service to rent for (`whatsapp`, `telegram`, `google`, ...). Full list: [flo-voice1.com/rent](https://flo-voice1.com/rent). |
| `period`                 | no       | `instant`                       | `instant` or `monthly`.                                             |
| `timeout-seconds`        | no       | `120`                           | Fail the step if no SMS arrives in this window.                     |
| `poll-interval-seconds`  | no       | `5`                             | How often to check for the SMS.                                     |
| `base-url`               | no       | `https://flo-voice1.com/api/v1` | Override only for testing against a different environment.          |

## Outputs

| Name           | Description                                                  |
| -------------- | -------------------------------------------------------------- |
| `rental-id`    | The id of the created rental.                                  |
| `phone-number` | The rented phone number, e.g. `+447700900123`.                 |
| `sms-sender`   | The sender field of the first SMS received.                    |
| `sms-body`     | The full raw text of the SMS. Extract the code yourself — formats vary per service, so this action doesn't guess a regex for you. |

## Also available

- [npm SDK](https://www.npmjs.com/package/sms-florin) — for use directly in your test code (not CI-only), includes a `waitForSms()` poller.
- [MCP server](https://www.npmjs.com/package/sms-florin-mcp) — for AI coding agents (Claude Code, Cursor, etc.) to rent numbers and read codes mid-task.

## License

MIT
