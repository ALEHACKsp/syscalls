# syscalls

<br> A simple single header file to use syscalls and regular usermode calls.

```
#include <Windows.h>
#include <iostream>

template<class T>
class Calls  {
private:
	void* _Function;
	const char* _Name;
public:
	Calls(const char* _NameT) {
		if (_NameT) {
			this->_Name = _NameT;
			this->_Function = reinterpret_cast<void*>(GetProcAddress(GetModuleHandleA("kernel32.dll"), _Name));

			if (!_Function) {
				throw std::exception("_Function returned NULL");
			}
		}
	}

	Calls(const char* _moduleName, const char* _NameT) {
		if (_moduleName && _NameT) {
			this->_Name = _NameT;
			this->_Function = reinterpret_cast<void*>(GetProcAddress(GetModuleHandleA(_moduleName), _Name));

			if (!_Function) {
				throw std::exception("_Function returned NULL");
			}
		}
	}

	__forceinline const char* GetName() const {
		return _Name;
	}

	__forceinline void* GetAddress() const {
		return _Function;
	}

	__forceinline const char* AddressToString() {
		char buf[64];
		sprintf_s(buf, "0x%llx", GetAddress());
		return buf;
	}

	template<typename ... Args> 
	__forceinline T Run(Args ... args)
	{
		return reinterpret_cast<T(*)(Args ...)>(_Function)(args...);
	}

};

#define SYSCALL(TYPE, NAME, ...) Calls<TYPE>("ntdll.dll", NAME).Run(__VA_ARGS__)

```
