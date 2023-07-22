---
title: EnhancedInput
slug: EnhancedInput
docs: https://docs.unrealengine.com/4.26/en-US/API/Plugins/EnhancedInput/
---

<br />

Create Mappings for your Actions with the new Enhanced Input Mapping.

<br />

***

<br />

*First* we need to create a "Input Mapping Context" and a InputAction in the Unreal Engine Editor.
After you've done that and you also implemented the code below, dont forget to assign the InputAction inside YourCharacter's Blueprint.
It should be in the details panel under "Input" or "YourGameName|Input"
<br />

<br />

**YourCharacter.h**

```cpp
...
#include "InputActionValue.h"
#include "YourCharacter.generated.h" // keep this always last

class AYourCharacter : public ACharacter
{
  GENERATED_BODY()
  // Define your Action, just name it as you named it in the Editor
  UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "GameName|Input", meta = (AllowPrivateAccess = "true"))
  class UInputAction* JumpAction; // in this example we'll call it JumpAction

  // In this example we'll implement the LineTrace inside the Tick
  protected:
  void Jumping(const FInputActionValue& Value);
  void StopJump(const FInputActionValue& Value);
}
```

<br/>

**YourCharacter.cpp**

```cpp
...
#include "Components/InputComponent.h"
#include "EnhancedInputComponent.h"
#include "EnhancedInputSubsystems.h"
...

AActor::AActor()
{
  // Your other stuff
}

void AYourCharacter::BeginPlay()
{
	// Call the base class  
	Super::BeginPlay();

	//Add Input Mapping Context
	if (APlayerController* PlayerController = Cast<APlayerController>(Controller))
	{
		if (UEnhancedInputLocalPlayerSubsystem* Subsystem = ULocalPlayer::GetSubsystem<UEnhancedInputLocalPlayerSubsystem>(PlayerController->GetLocalPlayer()))
		{
			Subsystem->AddMappingContext(DefaultMappingContext, 0);
		}
	}
}

// Input
void AYourCharacter::SetupPlayerInputComponent(class UInputComponent* PlayerInputComponent)
{
	// Set up action bindings
	if (UEnhancedInputComponent* EnhancedInputComponent = CastChecked<UEnhancedInputComponent>(PlayerInputComponent)) {
		
		//Jumping
    // You can use the Jump() function directly from the Character.h
		EnhancedInputComponent->BindAction(JumpAction, ETriggerEvent::Started, this, &ACharacter::Jump);
		EnhancedInputComponent->BindAction(JumpAction, ETriggerEvent::Completed, this, &ACharacter::StopJumping);

    // but we'll use our own function and then call it there
    // just to showcase how it looks like
    EnhancedInputComponent->BindAction(JumpAction, ETriggerEvent::Started, this, &AYourCharacter::Jumping);
		EnhancedInputComponent->BindAction(JumpAction, ETriggerEvent::Completed, this, &AYourCharacter::StopJump);
	}
}

// Input Logic comes here: when 
void AYourCharacter::Jumping(const FInputActionValue& Value)
{
  // do something here
  // e.g. set a bool bIsJumping or bInAir
  Jump(); // from Character.h

  // if you just want to test your input, you can Log something
  UE_LOG(LogTemp, Log, TEXT("Your Input works!"));
}

void AYourCharacter::StopJump(const FInputActionValue& Value)
{
  // same as above ^^
  StopJumping(); // also from Character.h
}

```
<br />

<br />
