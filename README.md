<img src="https://github.com/timoth-y/go-eventdriver/blob/main/docs/go-eventdriver.png?raw=true" alt="go-eventdriver" width="400px"/>

_Go package for handling local communication and logic responsibility segregation in an eventual way_

## Usage

```go
import (
	"context"

	"github.com/op/go-logging"
	"github.com/spf13/viper"
	"github.com/timoth-y/go-eventdriver"
)

func main() {
	// First, let's initialize eventdriver package:
	eventdriver.Init(
		eventdriver.WithLogger(logging.MustGetLogger("eventdriver_test")),
		eventdriver.WithBufferSize(viper.GetInt("event_channel_buffer_size")),
	)

	// Put handler somewhere in the use cases layer:
	eventdriver.SubscribeHandler("example.event.occurred", func(ctx context.Context, v interface{}) error {
		if payload, ok := v.(ExampleEventPayload); ok {
			// Do some awesome handling of the event here
			handleEvent(payload)

			return nil
		}

		return eventdriver.ErrIncorrectPayload
	})
	
	// Emit event in any place where such event can occur:
	eventdriver.EmitEvent(context.Background(), "example.event.occurred", ExampleEventPayload{
		Nice: true,
	})

	// Shutdown event loop gracefully.
	eventdriver.Close()
}
```
