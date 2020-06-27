# 手写Promise

```javascript
const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'
let PROMISE = null
function resolvePromise(promise, value, resolve, reject) {
  if(value instanceof MyPromise) {
      value.then(v => {
        resolvePromise(value, v, resolve, reject)
      })
  }
  let called = false
  if(value !== null &&(typeof value === 'object' || typeof value === 'function')) {
    try {
      const then = value.then
      if (typeof then === 'function') { 
        then.call(value, y => {
          if (called) return
          called = true
          resolvePromise(promise, y, resolve, reject)
        }, e => {
            if (called) return
            called = true
            resolve(promise, e, resolve, reject)
        })
      } else {
        resolve(value)
      }
    } catch (e) {
      if (called) return
      called = true
      reject(e)
    }
    
  } else {
    resolve(value)
  }
}


function MyPromise(executor) {
  this.status = PENDING
  this.value = null
  this.resolvedFns = []
  this.rejectedFns = []

  const resolve = value => {
    if(value instanceof  MyPromise) {
      value.then(resolve, reject)
    }
    setTimeout(() => {
      if (this.status === PENDING) {
        this.value = value
        this.status = FULFILLED
        this.resolvedFns.forEach(fn => fn(this.value))
      }
    }, 0)
  }
  const reject = value => {
    setTimeout(() => {
      if (this.status === PENDING) {
        this.value = value
        this.status = REJECTED
        this.rejectedFns.forEach(fn => fn(this.value))
      }
    }, 0)
  }
  try {
    executor(resolve, reject)
  } catch (e) {
    reject(e)
  }
}

MyPromise.prototype.then = function (onFulfilled, onRejected) {
  onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : d => d
  onRejected = typeof onRejected === 'function' ? onRejected : e => {throw *new* Error(e)}
  return (PROMISE = *new* MyPromise((resolve, reject) => {
    if (this.status === PENDING) {
      this.resolvedFns.push(() => {
        try{
          const ret = onFulfilled(this.value)
          resolvePromise(PROMISE, ret, resolve, reject)
        } catch (e) {
          reject(e)
        }
      })
      this.rejectedFns.push(() => {
        try{
          const ret = onRejected(this.value)
          resolvePromise(PROMISE, ret, resolve, reject)
        } catch (e) {
          reject(e)
        }
      })
    }
    if (this.status === FULFILLED) {
      setTimeout(() => {
        try {
          const ret = onFulfilled(this.value)
          resolvePromise(PROMISE, ret, resolve, reject)
        } catch (e) {
          reject(e)
        }
      });
    }
    if (this.status === REJECTED) {
      setTimeout(() => {
        try {
          const ret = onRejected(this.value)
          resolvePromise(PROMISE, ret, resolve, reject)
        } catch (e) {
          reject(e)
        }
      })
    }
  }))
}
MyPromise.resolve = function(d) {
  return (PROMISE = *new* MyPromise((resolve, reject) => {
    resolvePromise(PROMISE, d , resolve, reject)
  }))
}


```
#mydiary/javascript/advance#