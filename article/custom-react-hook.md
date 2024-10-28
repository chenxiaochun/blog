## 自定义实现 React hook

```tsx
import { useState } from 'react'

const myStates: [any, (v: any) => void] = []
let stateId = -1

const useMyState = (defaultValue: number) => {
  if (myStates[stateId]) {
    return myStates[stateId]
  }

  stateId += 1
  const setMyValue = (value: any) => {
    myStates[stateId][0] = value
  }
  const tuple = [defaultValue, setMyValue]
  myStates[stateId] = tuple
  return tuple
}

export function Test() {
  const [error, setError] = useState(null)
  const [value, setValue] = useMyState(0)
  const [refresh, setRefresh] = useState({})

  const handlePlus = () => {
    if (value >= 0) {
      setError(null)
    }
    setValue(value + 1)
    setRefresh({})
  }

  const handleMinus = () => {
    if (value < 1) {
      setError('Number should be positive.')
    } else {
      setValue(value - 1)
      setRefresh({})
    }
  }

  return (
    <div className="App" data-obj={refresh}>
      <label style={{ height: 30, display: 'block', color: 'red' }}>{error}</label>
      <div>
        <button onClick={handleMinus}>minus</button>
        <span style={{ margin: 30 }}>{value}</span>
        <button onClick={handlePlus}>plus</button>
      </div>
    </div>
  )
}
```