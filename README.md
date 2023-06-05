# Veh
Cool applications of a VEH.

# Control Flow Obfuscation
Theory:
* Create a VEH which handles divide by zero operations.
* Create a vector of function predicates.
* When a division by zero occurs, use the value in rax to index into the predicate array, or set rip to rax directly.

Pros:
* Control flow becomes very difficult if not impossible to follow without a debugger. 
* It requires analysis of the VEH itself and may confuse a lot of reverse engineers.

Cons: 
* This can be somewhat difficult to implement, especially without modification of a linker or compiler. 
* Often time stack pointer adjustment occurs at the start of the function and the div will jump before it resets leaving the stack mangled.
* A successful implementation of this might include: Handcrafted assembly or functions split into various branches and use this instead of jmp or call.



```cpp
LONG DivJumpHandler(EXCEPTION_POINTERS* exception_info)
{
	if (exception_info->ExceptionRecord->ExceptionCode == EXCEPTION_INT_DIVIDE_BY_ZERO)
	{
		auto dividend = exception_info->ContextRecord->Rax;
		if (dividend >= DIVJUMP_HANDLER_RANGE_START && dividend < DIVJUMP_HANDLER_RANGE_START + function_predicates.size())
		{
			exception_info->ContextRecord->Rip = (DWORD64)function_predicates[exception_info->ContextRecord->Rax - DIVJUMP_HANDLER_RANGE_START];
			return EXCEPTION_CONTINUE_EXECUTION;
		}
	}
}
```
