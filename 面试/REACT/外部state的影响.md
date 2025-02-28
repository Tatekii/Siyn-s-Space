In **React Concurrent Mode**, external state (such as global variables, event listeners, or external libraries like Redux, Zustand, or RxJS) can **cause inconsistencies** because React may **pause, resume, or restart rendering**. This means that external state updates **might not be in sync** with React’s rendering cycle.

## 解决防