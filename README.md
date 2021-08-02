# Splitgate

*I am aware it can be easier to use SDK and or ProcessEvent for everything but I am a catboy and I call the function without.* 

## Table Of Contents
* [Useful UE4 Functions Signatures](#Useful-Functions)
* [Useful UE4 Offsets](#Useful-Offsets)
* [Exploits](#Exploits)

## Useful Functions
```
UWorld *GWorld -> 48 8B 1D ? ? ? ? 48 85 DB  RIP relative 7

FMemory::Free -> 48 85 C9 ? 2E 53 48

UKismetSystemLibrary::GetObjectName -> 40 53 48 83 EC 20 48 8B D9 48 85 D2 75 45 33 C0 48 89 01 48 89 41 08 8D 50 05 E8 ? ? ? ? 8B 53 08 8D 42 05 89 43 08 3B 43 0C 7E 08 48 8B CB E8 ? ? ? ? 48 8B 0B 48 8D 15 ? ? ? ? 41 B8 ? ? ? ? E8 ? ? ? ? 48 8B C3 48 83 C4 20 5B C3 48 8B 42 18

UGameplayStatics::ProjectWorldToScreen -> 48 89 5C 24 08 48 89 6C 24 10 48 89 74 24 18 57 48 81 EC 20 01 00 00 41 0F B6 E9

UObject::ProcessEvent -> 40 55 56 57 41 54 41 55 41 56 41 57 48 81 EC ? ? ? ? 48 8D 6C 24 ? 48 89 9D ? ? ? ? 48 8B 05 ? ? ? ? 48 33 C5 48 89 85 ? ? ? ? 8B 41 0C 45

USkinnedMeshComponent::GetBoneMatrix -> 48 8B C4 55 53 56 57 41 54 41 56 41 57 48 8D 68 A1 48 81 EC ? ? ? ? 0F 29 78 B8 33 F6 44 0F 29 40

AController::LineOfSightTo -> 40 55 53 56 57 48 8D 6C 24 ? 48 81 EC ? ? ? ? 48 8B 05 ? ? ? ? 48 33 C4 48 89 45 E0 49 8B F8 48 8B DA 48 8B F1 48 85 D2 75 07 32 C0 E9

UStaticMesh::GetBoundingBox -> 48 83 EC 48 F3 0F 10 99 ? ? ? ?
```

## Useful Offsets
```
I will add some offset specific to the game later when I am fed.
This starts at uWorld because catboy, just set uworld yourself when start cheat. 

// Class Engine.World 
class UWorld
{
public:
    char                                               pad_0000[0x30];                                           // 0x0 (0x30)
    class ULevel*                                      PersistentLevel;                                          // 0x0030(0x0008) 
    char                                               pad_0001[0xE8];                                           // 0x38 (0xE8)
    class AGameStateBase*                              GameState;                                                // 0x120(0x08)
    char                                               pad_0002[0x10];                                           // 0x128 (0x10)
    TArray<class ULevel*>                              Levels;                                                   // 0x0138(0x0010)
    char                                               pad_0003[0x38];                                           // 0x148 (0x38)
    class UGameInstance*                               OwningGameInstance;                                       // 0x0180(0x0008)
    
    UWorld* GetWorld() {
        return (UWorld*)(*(ULONG_PTR*)(this));
    }
};

// Class Engine.Actor 
class AActor
{
public:
    char                                               pad_0000[0x118];                                          // 0x000 (0x118)
    class APawn*                                       Instigator;                                               // 0x118 (0x08) 
    char                                               pad_0001[0x10];                                           // 0x120 (0x10)
    class USceneComponent*                             RootComponent;                                            // 0x130 (0x08)
};

// Class Engine.Level
class ULevel
{
public:
    char                                               pad_0000[0x98];                                           // 0x0000 (0x98)
    TArray<AActor*>                                    Actors;                                                   // 0x0098 (0x10)
    char                                               pad_0001[0x10];                                           // 0x00A8 (0x10)
    class UWorld*                                      OwningWorld;                                              // 0x00B8 (0x08)
};

// Class Engine.SceneComponent
class USceneComponent
{
public:
    char                                               pad_0000[0x11c];                                          // 0x0 (0x11c)
    struct FVector                                     RelativeLocation;                                         // 0x011C(0x000C)
    struct FRotator                                    RelativeRotation;                                         // 0x0128(0x000C) 
    struct FVector                                     RelativeScale3D;                                          // 0x0134(0x000C)
    struct FVector                                     ComponentVelocity;                                        // 0x0140(0x000C)
};

// Class Engine.GameInstance
class UGameInstance
{
public:
    char                                               pad_0000[0x38];                                           // 0x0 (0x38)
    TArray<class UPlayer*>                             LocalPlayers;                                             // 0x0038(0x0010)
};


class AGameStateBase
{
public:
    char                                               pad_0000[0x2a0];                                           // 0x0 (0x2a0)
    TArray<struct APlayerState*>                       PlayerArray;                                               // 0x2a0(0x10)
};

// Class Engine.Controller 
class AController : public AActor
{
public:
    char                                               pad_0000[0xF0];                                           // 0x138 (0xF0)
    class APlayerState*                                PlayerState;                                              // 0x228(0x08)
    char                                               pad_0001[0x18];                                           // 0x230 (0x18)
    struct FName                                       StateName;                                                // 0x248(0x08)
    class APawn*                                       Pawn;                                                     // 0x250(0x08)
};

// Class Engine.PlayerController
class APlayerController : public AController
{
public:
    char                                               pad_0000[0x40];                                             // 0x258 (0x40)
    class UPlayer*                                     Player;                                                   // 0x298 (0x08)
    class APawn*                                       AcknowledgedPawn;                                         // 0x2a0 (0x08)
    char                                               pad_0001[0x18];                                           // 0x318 (0x18)
    class APlayerCameraManager*                        PlayerCameraManager;                                      // 0x2b8 (0x08)
};

// Class Engine.Player
class UPlayer
{
public:
    char                                               pad_0000[0x30];                                           // 0x0 (0x30)
    class APlayerController*                           PlayerController;                                         // 0x0030(0x0008)
};

// Class Engine.Pawn 
class APawn : public AActor
{
public:
	char                                               pad_0099[0x108];                                           // 0x138 (0x108)
	class APlayerState* PlayerState;                                                                              // 0x240 (0x08)
	char                                               pad_0001[0x10];                                            // 0x248 (0x10)
	class AController* Controller;                                                                                // 0x258 (0x08)
	char                                               pad_0002[0x20];                                            // 0x260 (0x20)
	class USkeletalMeshComponent* Mesh;                                                                           // 0x280 (0x08) 
};

// Class Engine.PlayerState
class APlayerState
{
public:
    char                                               pad_0000[0x280];                                          // 0x0 (0x280)
    class APawn*                                       PawnPrivate;                                              // 0x280(0x08)
    char                                               pad_288[0x78];                                            // 0x288(0x78)
    struct FString                                     PlayerNamePrivate;                                        // 0x300(0x10)
};

// Class Engine.PlayerCameraManager
class APlayerCameraManager : public AActor
{
public:
    char                                               pad_0000[0x1968];                                          // 0x138 (0x1968)
    struct FCameraCacheEntry                           CameraCachePrivate;                                        // 0x1aa0(0x5f0)
};

// Class Engine.SkeletalMeshSocket
class USkeletalMeshSocket
{
public:
    char                                               pad_0000[0x28];                                           // 0x0 (0x28)
    struct FName                                       SocketName;                                               // 0x0028(0x0008) 
    struct FName                                       BoneName;                                                 // 0x0030(0x0008) 
    struct FVector                                     RelativeLocation;                                         // 0x0038(0x000C) 
    struct FRotator                                    RelativeRotation;                                         // 0x0044(0x000C) 
    struct FVector                                     RelativeScale;                                            // 0x0050(0x000C)
};

// Class Engine.SkeletalMesh
class USkeletalMesh
{
public:
  // Poggers
};

// Class Engine.SkinnedMeshComponent UPDATED
class USkeletalMeshComponent
{
public:
    char                                               pad_0000[0x480];                                          // 0x0 (0x480)
    class USkeletalMesh*                               SkeletalMesh;                                             // 0x480(0x0008) 
};

```

## Exploits
```

	* ALL CREDITS TO HINNIE FOR THIS ONE I LOVE YOU *
VOID SkipLogin()
{
	auto tempWorld = WRLD->GetWorld();
	if (!tempWorld) return;

	auto tempGameInstance = tempWorld->OwningGameInstance;
	if (!tempGameInstance) return;

	auto tempLocalPlayer = tempGameInstance->LocalPlayers[0];
	if (!tempLocalPlayer) return;

	auto tempPlayerController = tempLocalPlayer->PlayerController;
	if (!tempPlayerController) return;

	auto LoginMenuWidget = read<uintptr_t>((uintptr_t)tempPlayerController + 0x590);

	// Class PortalWars.MainMenuPlayerController
	// UPortalWarsLoginMenuWidget* LoginMenuWidget; 0x590 Size - 0x8
	return reinterpret_cast<VOID(__fastcall*)(uintptr_t)>(LoginAddress)(LoginMenuWidget);
}
	Pattern For Variable LoginAddress -> 48 89 5C 24 ? 48 89 74 24 ? 55 57 41 56 48 8D 6C 24 ? 48 81 EC ? ? ? ? 48 8B F9 4C 8D




```

