---
layout: post
title:  "Dashing Ahead"
date:   2017-05-24 17:47:28 -0500
categories: update
---

It's a good thing I didn't talk about my implementation of dashing yesterday, since I enhanced and reworked a good portion of it today.  Most of my evening was spent getting a personal website hosted on AWS, and since I've never done anything like that before, it was a bit of a learning experience (turns out, a lot of the internet is just pointers).

Dashing in our game is a pretty simple mechanic.  When the player presses a button, I want to have our character move in a direction very quickly, but for a very short amount of time.  I'll walk through how this was implemented.  First, something we didn't talk about in the last post ({{ site.baseurl }}{% post_url 2017-05-23-first-real-days-work %}) is the idea of keybinding.  `Jump()` already had a default keybind, but since we're adding an entirely new mechanic, we needed to create a new one.  In UE4, that looks like this:

{% highlight cpp %}

void AFlintCharacter::SetupPlayerInputComponent(class UInputComponent* PlayerInputComponent)
{

PlayerInputComponent->BindAction("Dash", IE_Pressed, this, &AFlintCharacter::Dash);

}
{% endhighlight %}

There are other things in `SetupPlayerInputComponent`, but we're not terribly concerned with them yet.  This function is called whenever a player-character is instantiated within our game, and allows us to create bindings from actions or axes (via `BindAction()` and `BindAxis()`, respectively) to functions.  In this case, we give this binding a name for the editor (since we associate this binding with particular keys there), tell the engine to trigger this when the key is pressed (rather than released!), give it a delegate (which, from what I can tell, is almost always `this`?), and then tell it which function to call when the key is pressed.  In our case, `Dash()`.

Using `BindAxis()` is slightly different, but I'll cover that when we get to it.  Maybe when I get directional-dashing working?

Okay, so we've bound an action to a function.  Maybe we should set up that function and all the associated variables.

In the header, I added this:

{% highlight cpp %}

protected:

	// Called for sprint input
	UFUNCTION(BlueprintCallable)
	void Dash();

	// How fast the dash should be
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Movement")
	float DashMultiplier;

	// How long before dash comes off cooldown
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Movement")
	float DashCooldownTime;

private:

	// Check the cooldown state for dash
	bool GetDashCooldown();

	// Set the Dash cooldown start time
	void StartDashCooldown();

	// Set the dash cooldown state
	void SetDashCooldown(bool CooldownState);

	// The state of the dash cooldown
	bool bDashCooldown;

	// Handle for the dash cooldown timer
	FTimerHandle DashTimerHandle;

{% endhighlight %}

Most of these are extremely self-explanatory.  New to this block of code is `UFUNCTION`.  This works very similarly to `UPROPERTY`, in that it interacts with the Unreal Editor to allow us to call the function within blueprints, if we want to.  The other non-standard object here is `DashTimerHandle`.  This has the type `FTimerHandle`, which operates similarly to a file handle from Python.  It gives us a reference to a particular timer (which are global in UE4), and allows us to interact with it, as we'll see on the implementation side.

Speaking of which, here's how I did it:

{% highlight cpp %}

void AFlintCharacter::SetDashCooldown(bool CooldownState)
{
	bDashCooldown = CooldownState;
}

bool AFlintCharacter::GetDashCooldown()
{
	return bDashCooldown;
}

{% endhighlight %}

These are simple accessor functions to interact with `bDashCooldown`, which tells us whether the `Dash()` function is on cooldown.  So far, so Java.

{% highlight cpp %}

void AFlintCharacter::StartDashCooldown()
{
	bool bDashIsOnCooldown = false;
	FTimerDelegate DashTimerDelegate = FTimerDelegate::CreateUObject(this, &AFlintCharacter::SetDashCooldown, bDashIsOnCooldown);
	GetWorldTimerManager().SetTimer(DashTimerHandle, DashTimerDelegate, DashCooldownTime, false, -1.0f);
}

{% endhighlight %}

Okay, so here we see how we implement a basic cooldown.  We create a local variable to hold the state we want the cooldown to end up as, create an `FTimerDelegate` to handle the action we'd like to undertake when the timer triggers, and then set the timer using our `FTimerHandle`.  Of note: we set this to not loop, and set it to decrement by 1.  The `DashCooldownTime` variable we declared in the header is initialized in our constructor, which is fully editable in the Unreal Editor.  This lets us decide exactly how long the cooldown is on the fly.

{% highlight cpp %}

void AFlintCharacter::Landed(const FHitResult& Hit)
{
	Super::Landed(Hit);
	CurrentJumpCount = 0;
	SetDashCooldown(false);
	GetWorldTimerManager().ClearTimer(DashTimerHandle);
}

{% endhighlight %}

Last time we overrode the `Landed()` function within the `ACharacter` unreal class.  However, we didn't actually implement anything new there.  This time we do.  When the character lands from a jump, we want to reset our `Dash()` cooldown, and reset the timer.  So we do.

{% highlight cpp %}

void AFlintCharacter::Dash()
{
	//UE_LOG(LogTemp, Warning, TEXT("You tried to dash!"));
	// Cause the character to suddenly move in the direction it's currently traveling
	const FVector PlayerVelocity = GetVelocity();
	const float MaxWalkSpeed = GetCharacterMovement()->MaxWalkSpeed;
	const float DashSpeed = MaxWalkSpeed * (1.0f + AFlintCharacter::DashMultiplier);
	const float PlayerDirection = PlayerVelocity.X / fabs(PlayerVelocity.X);
	const FVector DashImpulse = FVector(PlayerDirection * MaxWalkSpeed * (1.0f + AFlintCharacter::DashMultiplier), 0.0, 0.0);
	//UE_LOG(LogTemp, Warning, TEXT("You tried to dash like this: %f %f"), MaxWalkSpeed, PlayerDirection)

	UE_LOG(LogTemp, Warning, TEXT("Dash cooldown is: %s"), bDashCooldown ? TEXT("TRUE") : TEXT("FALSE"))

	if (!bDashCooldown)
	{
		SetDashCooldown(true);
		GetCharacterMovement()->AddImpulse(DashImpulse, true);
		StartDashCooldown();
	}


}

{% endhighlight %}

Ahh, and here is our implementation of `Dash()`.  We do a bunch of basic vector math to find out how much force we want to add, and in what direction.  Then, we check to see if `Dash()` is on cooldown, and if it's not, we make sure it is, add an impulse to our character, and start the cooldown timer.

And that's it!

Simple as mud, right?

If you have any suggestions for this blog, feel free to reach out to me via email, or whichever other means you'd like.

[Kyle](http://www.kpitzen.io/)

[Flint](https://kpitzen.github.io/Flint/)

[Blog Source](https://github.com/kpitzen/kpitzen.github.io)

