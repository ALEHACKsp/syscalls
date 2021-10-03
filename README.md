# syscalls

<h1> A simple single header file to use syscalls and regular usermode calls. </h1>
<br>
<h2> Example usage: </h2>

```cpp
#include "syscalls.hpp"

int main()
{
    // Using the macro to simplify the call
    SYSCALL(NTSTATUS, "NtClose", GetCurrentProcess());

    auto call = Calls<DWORD/* Return type */>("GetCurrentProcessId"/* Function name */);

    DWORD processID = call.Run(); // Run / call the function (no args required) 

    if (processID > 0) 
    {
	std::cout << "[+] ProcessID: " << processID << std::endl;
	std::cout << "[+] GetCurrentProcessId: " << call.AddressToString() << std::endl;
    }

    std::cout << "==============================================" << std::endl;

    auto createToolhelp32snapshot = Calls<HANDLE>("CreateToolhelp32Snapshot");
    HANDLE snapshot = createToolhelp32snapshot.Run(TH32CS_SNAPPROCESS, 0); /* Run the function with args */

    std::cout << "[+] " << createToolhelp32snapshot.GetName() << std::endl; // Name of the function
    std::cout << "[+] Address: " << createToolhelp32snapshot.AddressToString() << std::endl << std::endl << std::endl; // Address (string) of the function

    if (snapshot) {
	    PROCESSENTRY32 pe32 = { sizeof(PROCESSENTRY32) };
		if (Process32First(snapshot, &pe32)) {
			while (Process32Next(snapshot, &pe32)) {
				std::cout << "[Process]: " << pe32.szExeFile << std::endl;
			}
		}
	}
	
    std::cout << "[+] Done!" << std::endl;
    std::cin.get();
}

```
